# PollReview

Análise de dados das eleições gerais de 2026 no Brasil (Presidente, 1º e 2º turno), usando dados abertos do TSE, em Jupyter Notebook local.

## Ambiente

- `venv/` com Python 3.12, kernel Jupyter registrado como **"pollreview"**.
- Stack: DuckDB (engine principal de consulta, lê os CSVs direto sem carregar tudo na RAM) + pandas + matplotlib/seaborn.
- **Sempre rodar `con.execute("PRAGMA disable_progress_bar")` logo após abrir a conexão DuckDB.** Sem isso, o DuckDB tenta renderizar a barra de progresso via `ipywidgets`, e o VSCode pode abrir um diálogo bloqueante pedindo pra baixar arquivos de um CDN de terceiros — isso trava a execução da célula de forma silenciosa (sem erro, sem uso de CPU) até alguém responder ao diálogo. Já aconteceu nesta sessão e custou bastante tempo de diagnóstico.

## Estrutura de dados

- `notebooks/01_eleicoes_2026_presidente.ipynb` — notebook principal.
- `data/votacao_secao_2026_BR.csv` — resultado agregado por seção eleitoral, nacional, **Presidente only**, 1º e 2º turno juntos (coluna `NR_TURNO`). Fonte: dataset `votacao_secao` do TSE, arquivo `BR` (não os arquivos por UF — esses trazem Governador/Senador/Deputados, não Presidente).
- `data/bu_web_2t/*.csv` — Boletim de Urna Web, granularidade de urna/seção, **2º turno**, um arquivo por UF + exterior (ZZ), só Presidente e Governador (únicos cargos com 2º turno em 2026). Tem campos de horário (`DT_ABERTURA`/`DT_ENCERRAMENTO`) e de contingência (`NR_URNA_EFETIVADA`/`DS_SECOES_AGREGADAS`) que o dataset `votacao_secao` não tem.
- `data/bu_web_1t/*.csv` — Boletim de Urna Web do **1º turno** (mesmo schema do 2T), um arquivo por UF + exterior (ZZ), mas cobre **todos os cargos de 2026** (Presidente, Governador, Senador, Dep. Federal, Dep. Estadual) — por isso é bem maior (~35GB extraído vs ~880MB do 2T). Baixado nesta sessão especificamente pra comparar biometria entre turnos. Filtrar sempre por `DS_CARGO_PERGUNTA = 'Presidente'` antes de qualquer agregação, senão conta votos de outros cargos.
- `data/leiame_tse.pdf` — layout oficial do dataset `votacao_secao`.

Ver `docs/tse_dados_abertos.md` para o dicionário de campos completo e os mecanismos de contingência de urna (troca vs. agregação de seção).

## Fontes oficiais (TSE Dados Abertos)

- Portal: `dadosabertos.tse.jus.br` (API CKAN em `/api/3/action/package_show?id=<dataset>`).
- CDN dos arquivos reais: `cdn.tse.jus.br/estatistica/sead/...`.
- Datasets usados:
  - `resultados-2026` (votação por seção, `votacao_secao_2026_<UF ou BR>.zip`)
  - `resultados-2026-boletim-de-urna` (BU Web, `bweb_<1t|2t>_<UF>_<timestamp>.zip`)
  - `resultados-2026-logs-do-sistema-de-preparacao-das-urnas-eletronicas-gedai-2-turno` (log Gedai — alocação original de urnas por seção; ainda não explorado nesta sessão, seria o cruzamento necessário pra confirmar troca de urna por `NR_URNA_EFETIVADA` divergente do planejado)

## Achados relevantes já confirmados

- **Prorrogação do horário de votação ocorreu só no 2º turno** (25/10/2026): decisão do ministro Zanin estendeu até 19h (horário de Brasília) apenas as seções que ainda estavam abertas às 17h. TSE reportou ~24.268 seções continuaram abertas depois das 17h10 (número deles, metodologia de corte absoluto nacional).
- **Mecanismos de contingência de urna são dois, e só um é confirmável com dados abertos:**
  1. Troca de urna na mesma seção (hardware com defeito, ex. teclado colado com superbonder) — **não confirmável**: precisaria cruzar `NR_URNA_EFETIVADA` com a urna originalmente alocada, mas o log Gedai (investigado nesta sessão) é sobre preparação de mídia de carga, não um registro de alocação seção↔urna. Só dá pra ver um proxy indireto: abertura de urna atípica (298 seções nacionalmente).
  2. Agregação de seção (urna quebrada sem reposição a tempo, eleitores redirecionados pra outra seção já funcionando) → `DS_SECOES_AGREGADAS` preenchido. **Confirmado: 16.142 seções no Brasil (2º turno)**.
- **Classificação implementada no notebook** (`notebooks/01_eleicoes_2026_presidente.ipynb`, coluna `tipo_ocorrencia_secao`, baseada em `data/bu_web_2t/*.csv`): detecção de prorrogação e abertura atípica usa mediana por UF (não tabela fixa de fuso horário) — nosso número de prorrogação (7.555 seções) é menor que o do TSE porque a metodologia é diferente (desvio da mediana por UF vs. corte absoluto 17h10 nacional), não é inconsistência de dados. Achado a investigar: RJ concentra 3.312 das 7.555 seções prorrogadas, desproporcional a SP/BA.
