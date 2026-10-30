# Dados Abertos do TSE — notas técnicas (Eleições 2026)

## Datasets usados

### 1. `votacao_secao_2026_<UF>.zip` / `votacao_secao_2026_BR.zip`

- Dataset CKAN: `resultados-2026`.
- URL: `https://cdn.tse.jus.br/estatistica/sead/odsele/votacao_secao/votacao_secao_2026_<UF>.zip`
- **Importante:** o arquivo `BR` traz só **Presidente** (nacional, 1º e 2º turno juntos). Os arquivos por UF (ex. `RR`) trazem Governador/Senador/Deputado Federal/Estadual — **não** têm Presidente.
- Separador `;`, encoding `latin1`, uma linha por seção × candidato.
- Colunas principais: `NR_TURNO`, `SG_UF`, `NM_MUNICIPIO`, `NR_ZONA`, `NR_SECAO`, `CD_CARGO`/`DS_CARGO`, `NR_VOTAVEL`/`NM_VOTAVEL`, `QT_VOTOS`.
- **Não tem** nenhum campo de horário de abertura/encerramento nem de contingência de urna (confirmado lendo o leiame oficial completo, `data/leiame_tse.pdf`).

### 2. `bweb_<1t|2t>_<UF>_<timestamp>.zip` (Boletim de Urna Web)

- Dataset CKAN: `resultados-2026-boletim-de-urna`.
- URL: `https://cdn.tse.jus.br/estatistica/sead/eleicoes/eleicoes2026/buweb/bweb_<1t|2t>_<UF>_<timestamp>.zip`
- Existe para **os dois turnos** (`bweb_1t_<UF>_*.zip` e `bweb_2t_<UF>_*.zip`) — achado corrigido nesta sessão: inicialmente supúnhamos que só existisse 2º turno, mas o 1T também está disponível, só que cobre **todos os cargos** (Presidente, Governador, Senador, Dep. Federal, Dep. Estadual), não só os que tiveram 2º turno — por isso o 1T é ~35GB extraído contra ~880MB do 2T (que só tem Presidente+Governador). Baixado em `data/bu_web_1t/*.csv` nesta sessão pra comparar biometria entre turnos.
- Granularidade: urna/seção × cargo × candidato.
- Campos relevantes que o `votacao_secao` não tem:
  - `DT_ABERTURA`, `DT_ENCERRAMENTO` — timestamp completo (data+hora) de abertura/fechamento da urna. Confirmado em dados reais: urnas em fuso diferente de Brasília fecham em horário local ajustado (ex. Roraima, UTC-4, fecha ~16h01 local ≈ 17h Brasília).
  - `NR_URNA_EFETIVADA` — número de série da urna **realmente usada**. Pode ser a original ou uma de contingência, se a original teve problema. Pra confirmar troca, precisa cruzar com o log de alocação original (dataset Gedai, ver abaixo) — não feito ainda.
  - `DS_SECOES_AGREGADAS` — lista (separada por `/`) das seções cujos votos foram agregados à seção principal daquela urna, quando a seção original não conseguiu votar na própria urna (redirecionamento de eleitores pra outra seção já funcionando). Valor `#NULO#` quando não houve agregação.
  - `CD_TIPO_URNA`/`DS_TIPO_URNA` — status de apuração da urna: 1-Apurada, 2-Não apurada, 3-Anulada e apurada em separado, 4-Anulada, 5-Não instalada. **Não é** sobre contingência, é sobre o status final de apuração.
- Leiame oficial completo vem dentro do zip (`_leiame-boletimurnaweb.pdf`), não commitado no repo (está em `data/bu_web_2t/` já extraído junto com os CSVs — cuidado, o diretório tem ~880MB de CSVs, confira `.gitignore` antes de qualquer commit).

### 3. Log Gedai — investigado e descartado para este propósito

- Dataset CKAN: `resultados-2026-logs-do-sistema-de-preparacao-das-urnas-eletronicas-gedai-2-turno` (e variante `-1-turno`).
- URL: `https://cdn.tse.jus.br/estatistica/sead/eleicoes/eleicoes2026/logsgedai/log_gedai_<1t|2t>_<UF>_<timestamp>.zip`
- **Estrutura:** cada `.zip` contém arquivos `.jez` por estação de trabalho (ex. `ZRR005STD01`), e cada `.jez` é na verdade um zip aninhado em 2 camadas (zip dentro de zip) até chegar num `.log` em texto puro (ISO-8859-1), com uma linha por evento, timestamp + categoria + mensagem + hash de integridade por linha.
- **Conteúdo real:** log operacional do software GEDAI-UE rodando na estação de preparação — eventos como "geração de mídia de carga", "verificação de assinatura", "serviço HotSwapFlash em execução", "cadastro de correspondências". É sobre o processo de **gravar os cartões de carga das urnas antes da eleição**, não um registro de qual urna foi alocada pra qual seção, nem de trocas ocorridas no dia da votação.
- **Conclusão: não serve para detectar troca de urna por contingência (mecanismo 1).** Não há, nos datasets abertos do TSE que localizamos, uma forma direta de confirmar `NR_URNA_EFETIVADA` divergente do planejado — só a agregação de seção (mecanismo 2) é verificável com os dados abertos disponíveis.

## Como consultar via API CKAN (sem adivinhar URL)

```bash
curl -s "https://dadosabertos.tse.jus.br/api/3/action/package_show?id=<nome-do-dataset>&_=$(date +%s)" | python3 -m json.tool
```

Atenção: a API tem cache de CDN que às vezes serve uma versão desatualizada do `package_show` (observado nesta sessão — mostrava só 1º turno pouco depois do 2º turno já estar disponível). Usar `package_search` ou adicionar um parâmetro de cache-busting (`&_=$(date +%s)`) se a contagem de `num_resources` parecer baixa demais.

## Mecanismos de contingência de urna — explicação

Dois mecanismos distintos, confirmados via leiame oficial + dados reais:

1. **Troca de urna (hardware), mesma seção.** Urna tem defeito físico (ex. teclado inutilizado). TSE pré-posiciona urnas de contingência; se chega a tempo, troca a máquina e a seção continua votando normalmente na urna nova. Em teoria apareceria como `NR_URNA_EFETIVADA` diferente do planejado — **mas não há dataset aberto do TSE com o "planejado" pra comparar** (o log Gedai é sobre preparação de mídia, não sobre alocação seção↔urna; investigado e descartado, ver seção acima). Não é detectável com os dados abertos disponíveis.
2. **Agregação de seção.** Se não há tempo/urna de contingência disponível, os eleitores que faltam votar são redirecionados pra uma urna de outra seção já em funcionamento (geralmente mesma zona eleitoral / mesmo local de votação). A seção "quebrada" não gera BU próprio; seus votos aparecem registrados no BU da seção que a recebeu, listada em `DS_SECOES_AGREGADAS`.

Confirmado nos dados: **16.142 seções no Brasil tiveram agregação no 2º turno de 2026** (contagem via `DS_SECOES_AGREGADAS != '#NULO#'` sobre todos os 28 arquivos de BU Web do 2º turno).

**Correção importante:** desse total, **1.216 são do exterior (ZZ)** e não são contingência real — são artefato estrutural de como o voto no exterior é organizado. No exterior, **92,82% de todas as seções aparecem como "agregadas"** (ex. Lisboa, Nova York e Paris: 100%), com horários de abertura/encerramento completamente normais (sem sinal de problema operacional). A explicação é administrativa: uma urna por cidade/consulado atende várias seções registradas no papel, consolidadas por desenho, não por defeito no dia. **A contagem de agregação por defeito de urna de verdade em território nacional é 16.142 − 1.216 = 14.926 seções**, não 16.142.

## Limite de eleitores por seção (`QT_APTOS`)

A legislação eleitoral fixa um teto administrativo de **400 eleitores aptos por seção**, aplicado quando a seção é criada/redistritada — não é um limite técnico da urna (que suporta volume bem maior).

Nos dados reais (BU Web, 2º turno, excluindo exterior), esse teto não é rígido no dia da votação: **17.656 seções (3,5% do Brasil)** têm `QT_APTOS` acima de 400 sem nenhuma agregação de contingência (máximo observado: 534). Isso é esperado — seções crescem organicamente (novos eleitores se cadastrando no mesmo local) entre um ciclo de redistritamento e outro; não é uma violação de regra.

**RJ concentra desproporcionalmente esse fenômeno: 20,09% das seções do estado passam de 400 eleitores**, mais que o dobro do segundo colocado (AL, 10,20%) e ~6x a média nacional (3,5%). Ver `docs/checagem_alegacoes_virais_2t.md` seção 13 para a tabela completa e a relação com as outras duas anomalias do RJ encontradas nesta análise (concentração de prorrogação e de agregação na Região Metropolitana).

**No exterior (ZZ), o padrão é diferente e já explicado** (ver seção de agregação acima): seções chegam a 800-930 eleitores por desenho administrativo (uma urna por cidade/consulado atendendo várias seções de papel), não por crescimento orgânico.

## Prorrogação do horário de votação (2º turno apenas)

- 25/10/2026: decisão do ministro Cristiano Zanin prorrogou a votação até 19h (horário de Brasília), mas **só nas seções que ainda estavam abertas às 17h** — seções já fechadas não reabriram.
- TSE: ~24.268 seções continuaram abertas depois das 17h10.
- Detecção nos dados: comparar `DT_ENCERRAMENTO` (ajustado pro fuso local de cada UF) contra o baseline normal de fechamento daquela UF — abordagem usada no notebook: calcular a mediana do horário de encerramento por UF e sinalizar outliers (>30min depois da mediana), em vez de tabela fixa de fuso horário (evita o problema dos ~11 municípios do sudoeste do Amazonas que seguem o fuso do Acre, difícil de mapear com precisão).
