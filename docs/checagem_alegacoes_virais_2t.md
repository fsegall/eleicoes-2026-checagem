# Checagem de alegações virais — 2º turno 2026 (Presidente)

Três alegações que circularam sobre o 2º turno (Lula × Flávio Bolsonaro), checadas com dados oficiais do TSE (Boletim de Urna Web, `data/bu_web_2t/*.csv`).

> **Aviso sobre o escopo.** Este documento testa consistência interna dos dados abertos exatamente como o TSE os publicou — não atesta que esses dados não foram alterados antes da divulgação, nem que a logística da eleição ocorreu sem falha ou irregularidade fora do que é capturado neles. Ausência de anomalia estatística nos dados divulgados não resolve se esses dados são, eles mesmos, um retrato fiel do que aconteceu nas urnas — pergunta que está logicamente a montante do próprio dado aberto, e que nenhuma análise como esta tem como responder. O que segue é o que os dados abertos disponíveis permitem verificar, nem mais, nem menos.

Resultado oficial nacional de referência:

| Candidato | Votos | % |
|---|---:|---:|
| Lula | 64.256.232 | 50,11% |
| Flávio Bolsonaro | 59.826.041 | 46,65% |
| Nulo | 2.723.305 | 2,12% |
| Branco | 1.430.496 | 1,12% |
| **Margem Lula** | | **+4.430.191** |

## 1. "Flávio teria vencido se só contassem as urnas que fecharam no horário previsto"

**Metodologia correta** (diferente da classificação estatística usada no notebook pra outros fins): horário oficial de encerramento por fuso — 17h Brasília-equivalente, ajustado por UF/município:
- 17h local: todas as UFs, exceto as abaixo
- 16h local (UTC-4): AM (exceto os 11 municípios abaixo), MT, MS, RO, RR
- 15h local (UTC-5): AC, e os 11 municípios do sudoeste do AM que seguem o fuso do Acre (Amaturá, Atalaia do Norte, Benjamin Constant, Eirunepé, Envira, Guajará, Ipixuna, Itamarati, Jutaí, Tabatinga, São Paulo de Olivença)
- Tolerância de 10 min sobre esse horário (mesma referência que o TSE usou — "17h10" — para não confundir atraso normal de fechamento com prorrogação de fato)

Resultado: **13.700 seções** (de 499.207) fecharam depois do horário oficial ajustado + tolerância.

**Número oficial do TSE (nota de imprensa, 27/10/2026) para comparação:** o TSE divulgou **34.393 votos** registrados nacionalmente depois das 17h no 2º turno (32.399 entre 17h-18h, 1.981 entre 18h-19h, 13 depois das 19h) — essa é a contagem de votos individuais no período de prorrogação, bem mais estrita que nossa métrica de seções inteiras. Por UF: SP 2.496 votos (6.093 seções abertas), RJ 2.349 (5.713 seções), BA 1.656 (3.787 seções), PE 577 (1.872 seções). (Um número preliminar de 8.673 votos/24.268 seções, divulgado na noite da eleição antes da apuração completa, foi superado por esse número final — não usar o preliminar.)

**Ressalva epistemológica importante:** o número de 34.393 é uma afirmação do TSE à imprensa — **não é verificável de forma independente com os dados abertos que usamos nesta análise.** O Boletim de Urna Web não tem timestamp por voto individual, só `DT_ENCERRAMENTO` (um único horário por urna) e `QT_VOTOS` (total agregado por candidato). Pra contar exatamente quantos votos entraram entre 17h-19h seria preciso o log interno da urna por voto, que não está nesse dataset público. Diferente disso, **nosso número de 3,72 milhões (votos em seções inteiras prorrogadas) é inteiramente reproduzível a partir dos arquivos que baixamos** — qualquer pessoa pode rodar a mesma query e chegar no mesmo valor. Isso não significa que o número do TSE esteja errado, só que, no contexto desta análise, é uma caixa-preta que estamos aceitando pela autoridade da fonte, não por verificação própria — diferente de todo o resto deste documento, calculado direto dos dados.

| | Lula | Flávio | Margem |
|---|---:|---:|---:|
| Nacional (oficial) | 64.256.232 | 59.826.041 | +4.430.191 |
| **Excluindo as 13.700 seções prorrogadas inteiras** | 62.142.542 | 58.221.653 | **+3.920.889** |

**Conclusão: alegação falsa.** Mesmo excluindo inteiramente (não só os votos "extras" depois das 17h — a seção toda, incluindo os votos da manhã, porque o BU não tem timestamp por voto individual) todas as seções que tiveram qualquer fechamento além do horário oficial, Lula continua na frente por quase 3,92 milhões de votos.

## 2. "A prorrogação favoreceu o Lula"

Dentro das 13.700 seções prorrogadas, o resultado pende mais pra Lula que a média nacional:

| | Lula | Flávio | Nulo | Branco |
|---|---:|---:|---:|---:|
| Nacional (referência) | 50,11% | 46,65% | 2,12% | 1,12% |
| Só seções prorrogadas | **54,85%** | 41,64% | 2,39% | 1,12% |

Margem gerada *dentro* desse recorte: Lula +509.302 votos.

**Conclusão: parcialmente verdadeira no sentido estatístico, mas irrelevante na prática.** As seções prorrogadas realmente pendem mais pra Lula que a média nacional — isso é verificável e não é "o oposto" do que a alegação sugere. Mas a margem absoluta gerada (+509 mil) é pequena demais pra ter decidido a eleição, mesmo somada às outras duas abaixo. **Isso não prova causalidade** — é mais provável que seja composição geográfica (ver achado sobre RJ abaixo) do que um efeito da prorrogação em si.

## 3. Urnas trocadas / seções agregadas (contingência)

**Correção pós-investigação do exterior (seção 10):** das 16.142 seções com `DS_SECOES_AGREGADAS` preenchido, **1.216 são do exterior (ZZ) e não são contingência real** — são artefato estrutural de como o voto no exterior é organizado (ver seção 10). A contingência real em território nacional é **14.926 seções**. Os números abaixo já excluem o exterior.

| | Lula | Flávio | Nulo | Branco |
|---|---:|---:|---:|---:|
| Nacional (referência) | 50,11% | 46,65% | 2,12% | 1,12% |
| Só seções agregadas (sem exterior) | **59,31%** | 37,90% | 1,91% | 0,88% |

Margem gerada dentro do recorte: Lula +826.618 (excluindo exterior; número original com exterior incluído era +853.459 — a diferença é pequena porque o exterior é uma fração pequena do total agregado). Excluindo as 14.926 seções inteiramente do total nacional: Lula ainda decisivo (margem não recalculada exatamente, mas a diferença de ~27 mil votos entre as duas versões não muda a conclusão).

## 4. "Votos sem biometria"

**Limitação metodológica importante:** não dá pra isolar quais votos vieram de eleitores com falha de biometria — o campo `QT_ELEI_BIOM_SEM_HABILITACAO` é uma contagem agregada por seção, não correlaciona com a escolha de candidato (voto é secreto por desenho; falha de biometria só aciona liberação manual pelo mesário, o voto é computado normalmente).

Escala nacional: 8.632.488 eleitores (6,73% de quem compareceu) precisaram de liberação manual. **Quase toda seção teve pelo menos uma falha** (só 448 mil votos, de 128 milhões, vêm de seções com zero falha) — então "com vs. sem falha" não é uma divisão útil. A divisão que importa é a **taxa** de falha por seção.

Decis de taxa de falha (1=mais baixa, 10=mais alta) × % Lula:

| Decil | Taxa de falha | % Lula | % Flávio |
|---|---|---:|---:|
| 1 | 0% – 2,9% | 49,88% | 46,85% |
| 5 | 5,3% – 6,1% | 49,39% | 47,32% |
| 8 | 8,0% – 9,4% | 50,41% | 46,41% |
| 10 | 11,8% – 99,1% | **53,26%** | 43,70% |

No decil mais extremo (seções com ≥11,78% de falha de biometria): Lula 6.185.375 (53,27%) × Flávio 5.073.824 (43,69%) — margem gerada **+1.111.551**, a maior das três. Excluindo esse decil inteiramente do total nacional: Lula +3.318.640 (ainda decisivo).

**Distribuição regional diferente das outras duas:** o decil de maior falha de biometria concentra proporcionalmente mais em **Piauí (26,15% das seções do estado) e Rio Grande do Sul (20,43%)** — não é dominado por RJ como a prorrogação (RJ fica em 6º lugar aqui, 11,6%). Sugere que biometria é um fenômeno distinto (possivelmente demográfico — idade, ocupação manual — não logístico/caos do dia), não a mesma causa raiz da prorrogação.

### 4.0 Biometria: 1º × 2º turno

Baixamos o Boletim de Urna Web do **1º turno** (`data/bu_web_1t/*.csv`, 28 arquivos, ~35GB extraído — não existia no projeto até esta sessão) especificamente pra comparar a taxa de falha de biometria entre turnos.

| Turno | Falhas de biometria | Comparecimento | Taxa |
|---|---:|---:|---:|
| 1º turno | 8.342.249 | 124.934.069 | 6,68% |
| 2º turno | 8.632.488 | 128.236.074 | 6,73% |

**Taxa estável entre turnos** (6,68% → 6,73%, variação de 0,05pp) — não há sinal de que a falha de biometria tenha aumentado ou diminuído de forma anômala do 1º pro 2º turno.

**Por UF, os líderes se repetem:** PI (8,65%→9,00%), RS (8,23%→8,45%) e RN (7,91%→8,10%) são o top 3 nos dois turnos. Algumas UFs sobem levemente (RJ +0,40pp), outras caem (MS -0,57pp, PR -0,49pp), sem padrão de disparo/desaparecimento entre turnos.

**Os locais se repetem fortemente, ao nível de seção:** casando as 497.897 seções entre os dois turnos, a correlação `taxa_1T × taxa_2T` é **0,82** (muito forte), e **66,4% das seções no decil mais alto de falha no 1º turno continuam no decil mais alto no 2º** (33.037 de 49.790). **Conclusão: biometria é uma característica estável do local (perfil etário/infraestrutura do eleitorado cadastrado naquela seção), não um evento aleatório ou pontual do dia da votação** — reforça a explicação estrutural já levantada, e não é compatível com manipulação pontual de um turno específico.

### 4.1 Teste adicional: biometria explicaria os outliers de seção (seção 5)?

Hipótese testada: se falha de biometria fosse usada pra fraudar resultado em seções específicas, essas seções deveriam ter taxa de falha de biometria **acima** da média, concentrada onde o resultado é anômalo.

- Correlação entre `|z-score|` da seção (ver seção 5, outliers dentro da zona) e taxa de falha de biometria daquela seção: **0,015** — praticamente nula.
- Taxa média de falha de biometria nos 139 outliers mais extremos (`|z| ≥ 5`): **5,64%**, abaixo da média nacional (6,83%).

**Conclusão: não há suporte pra essa hipótese.** As seções com resultado mais anômalo (presídios, aldeias indígenas) não têm taxa de falha de biometria acima da média — os dois fenômenos parecem independentes. Isso não prova impossibilidade de fraude isolada em algum ponto específico, mas derruba a ideia de um padrão sistemático de biometria-bypass concentrado nos resultados atípicos.

**Importante — por que biometria continua sendo o vetor de risco mais relevante, mesmo sem evidência estatística de exploração:** diferente de prorrogação/agregação/tamanho de seção (que são sobre *quando/onde* o voto aconteceu), falha de biometria é o único mecanismo testado nesta análise que reduz a verificação de identidade de quem vota ao nível mais fraco — a conferência visual do documento pelo mesário, no lugar da biometria. Isso é uma superfície real de risco (fraude de personificação), em tese, independente de qualquer sinal nos dados agregados. Os testes acima (4.1, 8) não encontraram padrão estatístico de exploração desse vetor, mas o motivo central permanece de pé: **não é possível auditar, com os dados abertos do TSE, em quem votaram especificamente as pessoas que passaram por liberação manual** — o voto é secreto por desenho, e não há forma de cruzar `QT_ELEI_BIOM_SEM_HABILITACAO` com escolha individual de candidato. "Não achamos evidência de exploração" é diferente de "esse canal foi auditado e está limpo" — é a lacuna estrutural mais honesta desta análise inteira.

## 5. Outliers individuais de seção (dentro da própria zona eleitoral)

Metodologia: para cada seção com ≥20 votos válidos (Lula+Flávio), z-score do `% Lula` em relação à média/desvio-padrão da própria zona eleitoral (mesma UF/município/zona, ≥15 seções na zona pra ter uma base estatística mínima).

**Resultado: os 30 maiores outliers nacionais (por `|z-score|`) são, sem exceção, de duas categorias bem conhecidas e documentadas:**
1. **Unidades prisionais/socioeducativas** (penitenciárias, CDPs, cadeias, unidades de internação de menores) — perfil socioeconômico do eleitorado carcerário historicamente diferente da zona ao redor.
2. **Aldeias/escolas indígenas** — comunidades que tendem a votar em bloco. Atenção: muitas aparecem com o nome na língua indígena (ex. `TAMÃKÃYÃ`, `TEKOA NHE ENGATU`, `TRINCHEIRA BACAJÁ`), não com a palavra genérica "indígena" — um filtro por palavra-chave simples não pega todas.

Depois de descontar essas categorias, sobra muito pouco: algumas seções em campus universitários (UFPR, UFG, UFSCar — perfil etário diferente, explicação plausível) e uma seção isolada sem explicação institucional óbvia: **Posto Ambulatorial de Saúde, Itapetininga/SP** (zona 52, seção 233) — numericamente irrelevante (73 votos válidos), mas vale registrar como ponto sem explicação óbvia encontrado.

**Lista completa dos 30 maiores `|z-score|` nacionais**, com o local de votação (`NM_LOCAL_VOTACAO` de `votacao_secao_2026_BR.csv`) — **nota: todos os 30 são do lado pró-Lula**, nenhum pró-Flávio aparece no topo absoluto:

| UF | Município | Zona/Seção | Local de votação | Lula | Flávio | % Lula (seção) | % Lula (zona) | z |
|---|---|---|---|---:|---:|---:|---:|---:|
| SP | Mogi Guaçu | 216/342 | Penitenciária Feminina de Mogi Guaçu | 174 | 9 | 95,08% | 31,58% | 11,87 |
| SP | Franca | 291/341 | Penitenciária de Franca | 41 | 3 | 93,18% | 38,93% | 9,50 |
| SP | Itapetininga | 52/233 | Posto Ambulatorial de Saúde | 61 | 12 | 83,56% | 33,49% | 9,18 |
| SP | Taubaté | 141/409 | CDP Dr. Félix Nobre de Campos | 27 | 4 | 87,10% | 36,17% | 9,15 |
| SP | São Paulo | 248/887 | UI Itaquera - Fundação CASA | 39 | 3 | 92,86% | 56,46% | 9,11 |
| PA | Ananindeua | 72/629 | Unidade de Custódia e Reinserção Feminina | 31 | 3 | 91,18% | 50,82% | 9,08 |
| GO | Anápolis | 144/342 | Unidade de Polícia Penal Regional | 30 | 11 | 73,17% | 27,50% | 8,27 |
| DF | Brasília | 21/354 | UNIRE - Unidade de Internação Recanto das Emas | 53 | 9 | 85,48% | 43,77% | 8,24 |
| SP | São Paulo | 325/685 | Internato Pirituba | 48 | 4 | 92,31% | 50,25% | 8,01 |
| SP | Votorantim | 220/270 | Penitenciária Feminina de Votorantim | 67 | 9 | 88,16% | 40,52% | 7,96 |
| MT | Guarantã do Norte | 44/284 | Aldeia Sankorassan | 145 | 0 | 100,00% | 18,46% | 7,89 |
| DF | Brasília | 6/454 | Unidade de Internação de Planaltina | 27 | 4 | 87,10% | 46,86% | 7,61 |
| RR | Boa Vista | 5/278 | Escola Municipal Indígena Vicente André da Silva | 186 | 78 | 70,45% | 23,19% | 7,57 |
| RO | Ji-Paraná | 30/125 | Ikolen - Posto Indígena | 234 | 2 | 99,15% | 27,06% | 7,38 |
| MT | Rondonópolis | 46/380 | Posto Indígena Tadarimana | 236 | 22 | 91,47% | 36,61% | 7,37 |
| ES | Cachoeiro de Itapemirim | 48/318 | CDPCI | 63 | 11 | 85,14% | 35,08% | 7,36 |
| SP | Ribeirão Preto | 305/333 | Centro de Detenção Provisória | 42 | 10 | 80,77% | 37,90% | 7,20 |
| RO | Ji-Paraná | 30/138 | Ikolen - Posto Indígena | 228 | 6 | 97,44% | 27,06% | 7,20 |
| SP | Caraguatatuba | 206/237 | CDP de Caraguatatuba | 185 | 22 | 89,37% | 42,84% | 7,19 |
| AC | Cruzeiro do Sul | 4/457 | Escola Tamãkãyã | 137 | 0 | 100,00% | 29,59% | 7,09 |
| PR | Londrina | 41/428 | Cadeia Pública Feminina | 29 | 10 | 74,36% | 30,14% | 7,09 |
| SC | Tubarão | 99/246 | Presídio Masculino de Tubarão | 19 | 6 | 76,00% | 25,13% | 7,08 |
| MT | Juína | 35/321 | Aldeia Indígena Kotakowinakwa/Doloikwa | 337 | 15 | 95,74% | 27,79% | 6,97 |
| PA | Ananindeua | 72/628 | Unidade de Custódia e Reinserção | 27 | 6 | 81,82% | 50,82% | 6,97 |
| RO | Ji-Paraná | 30/165 | Aldeia Pay Gap | 78 | 4 | 95,12% | 27,06% | 6,97 |
| DF | Brasília | 16/2022 | UIBRA - Unidade de Internação de Brazlândia | 24 | 7 | 77,42% | 43,60% | 6,96 |
| SC | Blumenau | 3/765 | Presídio Regional de Blumenau | 43 | 23 | 65,15% | 27,66% | 6,95 |
| RJ | Angra dos Reis | 147/286 | Colégio Indígena Estadual Karai Kuery Renda | 159 | 2 | 98,76% | 36,57% | 6,94 |
| AC | Cruzeiro do Sul | 4/301 | Escola Tamãkãyã | 118 | 2 | 98,33% | 29,59% | 6,92 |
| SC | Araquari | 27/247 | EIEF Kirikue Nhemboea | 63 | 3 | 95,45% | 30,82% | 6,85 |

Contagem: **14 prisional/socioeducativo, 14 indígena (aldeia/escola/posto), 1 Internato (Pirituba, provável unidade socioeducativa) e 1 sem categoria institucional óbvia** (Itapetininga). Bate exatamente com a descrição qualitativa já registrada, agora com a lista completa e nominal.

### 5.1 Maior diferença absoluta Lula×Flávio por seção, e concentração regional

Diferente da seção 5 (outliers relativos à própria zona, via z-score), aqui olhamos a margem absoluta (`lula - flavio`) por seção em valor bruto, sem normalizar pela zona — top 500 seções mais pró-Lula e top 500 mais pró-Flávio, agrupadas pelas 5 macrorregiões do Brasil.

| Top 500 mais pró-Lula | N seções | % do grupo |
|---|---:|---:|
| Nordeste | 354 | 70,8% |
| Norte | 125 | 25,0% |
| Centro-Oeste | 13 | 2,6% |
| Sul | 7 | 1,4% |
| Sudeste | 1 | 0,2% |

| Top 500 mais pró-Flávio | N seções | % do grupo |
|---|---:|---:|
| Sul | 303 | 60,6% |
| Centro-Oeste | 167 | 33,4% |
| Sudeste | 11 | 2,2% |
| Nordeste | 10 | 2,0% |
| Norte | 9 | 1,8% |

**Nordeste+Norte concentram 95,8% das seções mais pró-Lula; Sul+Centro-Oeste concentram 94,0% das mais pró-Flávio.** Bate exatamente com o mapa eleitoral já conhecido do Brasil — funciona como teste de sanidade dos dados, não é um achado novo.

**As 10 seções individuais mais extremas de cada lado**, com local:

- **Mais pró-Lula:** dominadas pelo interior do Amazonas na fronteira (Benjamin Constant, Amaturá, São Paulo de Olivença, Tabatinga — os mesmos municípios do "fuso do Acre" usados na metodologia de prorrogação) e pequenas cidades do interior nordestino (Pesqueira/PE, Porto Real do Colégio/AL, Novo Horizonte/BA). Caso mais extremo: Tabatinga/AM zona 36 seção 61 — 354 Lula × 0 Flávio.
- **Mais pró-Flávio:** dominadas por Rio Grande do Sul e Santa Catarina (colonização alemã/italiana — Arroio do Padre, Fagundes Varela, Massaranduba, Braço do Norte, Canguçu) e **Sorriso/MT, que aparece 4 vezes no top 10** (capital nacional da soja, um dos municípios mais consistentemente de direita do Brasil em qualquer eleição).

**Conclusão: mesmo mecanismo sociológico da seção 5 (comunidades pequenas e culturalmente homogêneas votando em bloco), sem o componente institucional (presídios/aldeias formais) — aqui é geografia política conhecida (fronteira amazônica/interior nordestino pró-PT vs. colonização europeia/agronegócio pró-direita), não uma anomalia.**

### 5.2 Censo completo de outliers por zona (não só o top 30)

Extensão da seção 5: em vez de olhar só os 30 maiores `|z-score|`, aplicamos o limiar `|z|≥3` (mesmo usado na seção 8 pra "ilha lulista") a todas as 488.048 seções elegíveis (zona com ≥15 seções, seção com ≥20 votos válidos), pra ter a contagem completa de seções que destoam fortemente da própria zona — candidatas a "parecer não pertencer" àquela zona.

**2.275 seções (0,47% das elegíveis) têm `|z|≥3`** — assimétrico: **1.763 "ilhas pró-Lula" (z≥3)** vs. **512 "ilhas pró-Flávio" (z≤-3)**, proporção de ~3,4:1.

**Categorização por nome do local de votação** (cruzado com `NM_LOCAL_VOTACAO` de `votacao_secao_2026_BR.csv`):

| Categoria | Pró-Lula | Pró-Flávio |
|---|---:|---:|
| Prisional/socioeducativo | 67 | 1 |
| Indígena (nome explícito) | 52 | 0 |
| Universidade/campus | 24 | 7 |
| Militar | 1 | 2 |
| Sem categoria identificada por palavra-chave | 1.619 | 502 |

A maioria não bate com palavra-chave simples, mas **isso não equivale a "sem explicação"** — a seção 5 já registrou que aldeias indígenas costumam aparecer com nome em língua nativa (ex. `TAMÃKÃYÃ`), que um filtro de palavra-chave não captura. O piso de 146 seções institucionalmente identificadas é só um piso, não o total real.

**Margem gerada + teste de exclusão (metodologia padrão desta análise):**
- Dentro dos 2.275 outliers: Lula 61,45% (277.563×174.135), margem **+103.428** (2,3% da margem nacional).
- Excluindo os 2.275 outliers do total nacional: margem cai de +4.429.832 para **+4.326.404** — queda de ~103 mil votos, numericamente irrelevante pro resultado.

**Por UF**, maior volume absoluto em SP (470), MG (241), RJ (188), PR (141) — reflete população/número de seções de cada UF, não concentração relativa desproporcional (diferente de RJ nas seções 9/13/14, aqui RJ não se destaca como outlier de UF).

**Conclusão: outliers fortes de zona existem, são raros (0,47%), concentram no mesmo tipo de local institucional já conhecido (presídios, aldeias indígenas, campi), pendem mais pra Lula por motivo sociológico já identificado — sem escala pra mover o resultado.**

## 6. Delta 1º × 2º turno por seção (Lula × Flávio)

Comparação do `% Lula` (entre Lula+Flávio) por seção entre 1º e 2º turno, usando `votacao_secao_2026_BR.csv` (mesmo dataset pros dois turnos, evita problema de casamento de seção entre datasets diferentes).

**Por percentual (seções com ≥20 votos válidos nos dois turnos):** maiores swings são em municípios pequenos do interior de AM/AC/PA (ex. Feijó/AC +41,55pp, Barcelos/AM +36,41pp, Manoel Urbano/AC -35,49pp) — mas com N baixo (94-270 votos), grandes variações percentuais podem vir de poucas dezenas de votos mudando. Sinal fraco, ruidoso.

**Por volume absoluto de votos (margem Lula-Flávio, delta entre turnos):** padrão mais robusto e concentrado — **11 das 30 maiores mudanças de margem do país são seções de Salvador/BA**, todas no mesmo formato: Lula ganha ~70-90 votos, Flávio fica praticamente parado (ex. 59→58, 94→82, 70→70, 85→74).

**Confirmado com dados de comparecimento (cidade toda, 5.124 seções de Salvador):**
- Comparecimento: 1.561.593 (1º turno) → 1.647.069 (2º turno) — +85.476 votos.
- Ganho de Lula na cidade: +209.714. Ganho de Flávio: +30.564.
- Correlação entre aumento de comparecimento por seção e ganho de Lula: **0,77** (forte). Com ganho de Flávio: **0,20** (fraca).

**Conclusão: não é anomalia, é dinâmica normal de 2º turno.** O crescimento de Lula em Salvador vem de (a) aumento de comparecimento concentrado em eleitores que foram pra Lula, e (b) redistribuição de votos de candidatos eliminados no 1º turno (Caiado, Zema etc.) — Flávio não perde votos em nenhum momento, só não cresce proporcionalmente. Plausível dado o histórico de força do PT na Bahia; não investigamos causa política específica além disso.

### 6.1 O que de fato explica a virada de margem entre turnos (e por que não foi o Nordeste)

Hipótese testada: será que o aumento de comparecimento no Nordeste entre turnos foi o fator decisivo da eleição? Resultado: **não**, por dois motivos.

| | Δ comparecimento (1T→2T) | Ganho Lula | Ganho Flávio |
|---|---:|---:|---:|
| Nordeste | +826.761 | +2.955.848 | +397.763 |
| Resto do Brasil | +2.133.478 | +7.420.846 | +3.323.775 |

1. **O Nordeste nem tem o maior crescimento de comparecimento em termos absolutos** — o resto do país cresceu mais (região menor em população).
2. **Dentro do próprio Nordeste, o ganho de Lula (+2,96M) é muito maior que o crescimento de comparecimento (+827 mil)** — a maior parte do ganho vem de transferência de voto de candidatos eliminados no 1º turno, não de eleitor novo. O mesmo vale pro resto do Brasil.

**O que de fato explica a virada de margem (Flávio +2,22M no 1º turno → Lula +4,43M no 2º turno, swing de 6,66M) é a consolidação do 1º turno como um todo, nacionalmente — não um efeito regional do Nordeste.** Decompondo o "pool" de votos que ficou disponível entre turnos:

| Fonte do pool | Valor | % do pool |
|---|---:|---:|
| Votos de candidatos eliminados no 1T (Caiado, Zema, Cury, etc.) | 9.321.993 | 66% |
| Crescimento de comparecimento (eleitor novo) | 2.960.239 | 21% |
| Queda de votos nulos | 945.698 | 7% |
| Queda de votos em branco | 870.302 | 6% |
| **Total** | **14.098.232** | 100% |

Lula cresceu +10.376.694, Flávio +3.721.538 (diferença = swing de 6.655.156). Transferência de candidato eliminado é o maior componente isolado (66%), mas comparecimento novo e conversão de nulo/branco juntos são quase um terço do pool — não dá pra isolar, com os dados públicos, quanto de cada componente foi especificamente pra Lula vs Flávio (não há voto individual rastreado entre turnos). **Conclusão: o achado de Salvador é um estudo de caso local válido, mas não generaliza pra "o Nordeste decidiu via comparecimento" — a virada de margem é uma consolidação nacional do 1º turno, com múltiplas fontes, não um efeito regional isolado.**

**Achado complementar — voto no exterior, 1º×2º turno completo:** com o BU Web do 1º turno agora disponível, dá pra ver o quadro completo do exterior (`SG_UF='ZZ'`, `votacao_secao_2026_BR.csv`):

| Turno | Total de votos | Lula | Flávio | Margem |
|---|---:|---:|---:|---:|
| 1º turno | 341.766 | 157.387 (46,05%) | 143.900 (42,11%) | +13.487 |
| 2º turno | 381.443 | 200.154 (52,48%) | 171.497 (44,96%) | +28.657 |

Comparecimento no exterior cresceu +11,6% entre turnos (bem acima da média nacional, +2,4%), e a margem pró-Lula mais que dobrou.

**De onde vem esse crescimento de margem — mesma decomposição da seção 6.1, agora só pro exterior:**

| Fonte do pool (exterior) | Valor | % do pool |
|---|---:|---:|
| Votos de outros candidatos eliminados no 1T (Cury, Renan, Caiado, Zema, etc.) | 29.718 | 42,2% |
| Crescimento de comparecimento (eleitor novo) | 39.677 | 56,4% |
| Queda de votos em branco | 1.141 | 1,6% |
| Variação de votos nulos | -172 | -0,2% |
| **Total do pool** | **70.364** | 100% |

Os "outros candidatos" sozinhos (29.718) já somam quase o dobro do swing necessário (+15.170, de +13.487 pra +28.657) — a aritmética fecha com folga mesmo sem o crescimento de comparecimento. Diferente do padrão nacional (seção 6.1, onde outros candidatos eram 66% do pool e comparecimento só 21%), no exterior o **crescimento de comparecimento pesa mais (56,4%)** — proporcional ao crescimento de comparecimento ter sido bem maior ali (+11,6% vs +2,4% nacional). Do pool de 70.364 votos em jogo, Lula capturou 60,8% e Flávio 39,2% — essa diferença de captura gera o swing. Por cidade, o padrão é de **polarização, não de troca de lado** — cada região intensificou a direção que já tinha no 1º turno:

| Ficaram mais pró-Lula (já eram) | Δ margem | Ficaram mais pró-Flávio (já eram) | Δ margem |
|---|---:|---|---:|
| Lisboa | +2.126 | Nagóia | -949 |
| Porto | +1.728 | Boston | -894 |
| Paris | +1.420 | Tóquio | -680 |
| Londres | +1.212 | Miami | -649 |
| Dublin | +952 | Orlando | -508 |

Europa inteira (Lisboa, Porto, Paris, Londres, Dublin, Munique, Berlim, Barcelona) ficou mais pró-Lula; EUA/Japão (Miami, Orlando, Boston, Tóquio, Nagóia, Hamamatsu) e Ciudad del Este (fronteira agro no Paraguai) ficaram mais pró-Flávio. Nenhuma cidade trocou de lado — é o mesmo efeito de consolidação/polarização de 2º turno visto no resto do país (seção 6.1), não uma anomalia do exterior.

### 6.2 O swing do Nordeste é maior, em votos, que qualquer mecanismo operacional testado

Decompondo o swing nacional (seção 6.1) por região: quanto cada uma contribuiu, em termos líquidos (ganho Lula − ganho Flávio, 1T→2T), pra virada de margem de 6.655.156 votos.

| Região | Ganho Lula | Ganho Flávio | Swing líquido | % da margem nacional (4.430.191) |
|---|---:|---:|---:|---:|
| Nordeste | +2.955.848 | +397.763 | **+2.558.085** | **57,7%** |
| Resto do Brasil | +7.420.846 | +3.323.775 | +4.097.071 | 92,5% |

**O swing líquido do Nordeste sozinho (+2.558.085) é maior que qualquer um dos três mecanismos "suspeitos" testados neste documento** — maior que prorrogação (+509.302, seção 2), agregação (+826.618, seção 3) e biometria (+1.111.551, seção 4) somados (+2.447.471). Numericamente, é o maior componente pró-Lula identificado em toda a análise.

**Mas isso não é comparável aos outros três no sentido que importa.** Prorrogação/agregação/biometria testam se um **incidente operacional** (algo que, na ausência do incidente, não teria acontecido daquele jeito) inflou o resultado. O swing do Nordeste não é um incidente — é consolidação política normal de 2º turno (transferência de voto de candidatos eliminados + crescimento de comparecimento) numa região que já era maciçamente pró-Lula desde o 1º turno. Para ilustrar o quanto isso é estrutural, não um evento do dia da eleição: **excluindo o Nordeste inteiramente do total nacional** (todo o voto, não só o swing), o resultado viraria Flávio +9.223.505 — mas esse teste não mede fraude ou irregularidade, mede o óbvio (uma região eleitoralmente concentrada pesa muito quando removida do total, o mesmo valeria pra qualquer reduto eleitoral de qualquer candidato, em qualquer eleição).

**Conclusão: o maior "mecanismo" que gerou a margem de Lula foi geografia política real (Nordeste + consolidação do 1º turno em todo o país), não nenhuma das três alegações de fraude testadas — que seguem sendo, individualmente e somadas, pequenas demais pra terem decidido.**

## 7. Teste de mecanismo causal: biometria/comparecimento explicam a prorrogação?

Hipótese: seção com mais falha de biometria ou mais comparecimento teria fila maior e mais chance de ainda estar votando às 17h (prorrogação por volume de demanda, não por decisão política).

| Prorrogada | N seções | Taxa média de falha de biometria | Taxa média de comparecimento |
|---|---:|---:|---:|
| Não | 485.478 | 6,89% | 80,94% |
| Sim | 13.700 | 6,41% | 79,73% |

Correlação taxa de biometria × prorrogação: -2,0%. Correlação comparecimento × prorrogação: -3,4%. Ambas fracas e com sinal **negativo** (oposto do esperado pela hipótese).

**Conclusão: hipótese refutada.** Seções prorrogadas não têm mais falha de biometria nem mais comparecimento que as normais — se algo, têm um pouco menos. Prorrogação não parece ser explicada por volume de demanda/fila na própria seção. Explicação alternativa mais plausível (não testada com dados, baseada no que já sabemos da cobertura jornalística): a decisão judicial saiu às 17h02 mas o TSE só avisou as seções às 17h49 — a variável decisiva pode ter sido **velocidade de repasse da ordem judicial** (logística/comunicação por região), não demanda operacional da seção.

## 8. "Ilhas lulistas" em território bolsonarista × biometria

Teste mais específico que a seção 4.1: restringindo só às seções com z-score fortemente positivo (`z ≥ 3`, i.e., bem mais Lula que a própria zona) **dentro de zonas onde a média já pende pro Flávio** (`media_zona < 50%`) — essas são as "ilhas lulistas" literais, não qualquer outlier.

| Grupo | N seções | Taxa média de falha de biometria | % Lula médio |
|---|---:|---:|---:|
| Resto da zona bolsonarista | 246.742 | 6,65% | 39,26% |
| Ilha lulista (z≥3) | 1.220 | 6,38% | 66,09% |

**Conclusão: sem correlação, de novo.** As ilhas lulistas têm taxa de biometria igual (levemente menor) à do resto da zona bolsonarista ao redor. Confirma o resultado da seção 4.1 num recorte mais afiado — biometria não explica os bolsões de Lula em território de Flávio.

## 9. RJ por município: onde está a concentração de prorrogação?

RJ domina a prorrogação nacional (seção 1) — mas é a capital ou espalhado? Breakdown por município (prorrogação = `DT_ENCERRAMENTO` após 17h10, mesmo critério rigoroso da seção 1; RJ é fuso padrão de Brasília, sem ajuste necessário):

| Município | Total seções | Prorrogadas | % |
|---|---:|---:|---:|
| Rio de Janeiro (capital) | 12.635 | 2.481 | 19,64% |
| Niterói | 1.327 | 367 | 27,66% |
| Petrópolis | 804 | 302 | 37,56% |
| Itaboraí | 540 | 247 | **45,74%** |
| São Gonçalo | 2.019 | 216 | 10,70% |
| Campos dos Goytacazes (interior, fora da RM) | 1.190 | 15 | 1,26% |

Total RJ: 4.036 de 37.675 seções prorrogadas (10,7%) — número com o critério rigoroso de fuso, maior que os 3.312 que tínhamos achado antes com a mediana estatística (métodos diferentes, não é inconsistência).

**Conclusão: concentrado na Região Metropolitana do Rio, não só na capital.** A capital tem o maior volume absoluto (61% do total do estado), mas proporcionalmente **Itaboraí (45,74%) e Petrópolis (37,56%) foram mais afetadas que a própria capital** (19,64%). Fora da região metropolitana, o fenômeno praticamente desaparece (Campos dos Goytacazes: 1,26%). Isso é consistente com a hipótese de logística de comunicação da ordem judicial se propagando de forma desigual a partir do eixo metropolitano — Itaboraí e Petrópolis não têm perfil político parecido entre si, o que pesa contra uma explicação política e a favor de uma explicação geográfica/logística.

## 10. Anomalia do exterior (ZZ) em "seções agregadas" — não é contingência

Investigando por que ZZ aparecia no top 5 de agregação (seção 3 original): **92,82% de todas as seções do exterior (1.216 de 1.310) aparecem como "agregadas"** — não é um problema isolado, é praticamente universal (Lisboa, Nova York, Paris, Milão: 100%; Tóquio: 97,7%; Boston: 97%).

Horários de abertura/encerramento no exterior são completamente normais (ex. Abu Dhabi, Amsterdã: abertura 08h00, encerramento 17h00-17h08) — sem nenhum sinal de problema operacional no dia.

**Conclusão: é artefato estrutural, não contingência.** No exterior, uma urna por cidade/consulado atende várias seções registradas no papel — a consolidação é por desenho administrativo (menos urnas físicas que seções formais, já que o eleitorado no exterior é menor e mais disperso), não por defeito de urna no dia da votação. Isso corrigiu a contagem nacional de contingência real: **14.926 seções** (não 16.142 — ver seção 3, já corrigida acima).

## 11. Governador: o padrão se generaliza?

Seis estados tiveram 2º turno de Governador em 2026: AC, AM, DF, ES, RN, TO (RJ não teve). Teste: nas seções prorrogadas/agregadas de cada estado, o candidato que já lidera no estado ganha força (como Lula ganhou em todo teste nacional) ou não?

| UF | Líder do estado | % geral | % em prorrogadas | % em agregadas |
|---|---|---:|---:|---:|
| AC | Mailza Assis | 60,14% | 55,65% | 62,95% |
| AM | Omar Aziz | 58,18% | 61,89% | 68,69% |
| DF | Celina Leão | 53,23% | 52,98% | 55,28% |
| ES | Lorenzo Pazolini | 47,88% | 48,04% | 45,77% |
| RN | Cadu de Lula | 48,13% | 48,65% | 50,45% |
| TO | Vicentinho Júnior | 58,96% | 58,06% | 61,60% |

**Conclusão: não generaliza de forma uniforme.** Diferente do Presidente (onde prorrogação e agregação penderam pra Lula em *todo* teste nacional feito), aqui o efeito é misto — sobe em alguns estados/recortes, cai em outros. Isso enfraquece a hipótese de "seção com incidente operacional favorece mecanicamente quem já lidera" como regra geral — o padrão achado pro Presidente parece mais específico à composição geográfica/demográfica que correlaciona com Lula nacionalmente (RJ, Salvador, etc.), não uma lei física do tipo "incidente = vantagem pro líder".

## 12. Relatório atribuído a Joey Mannarino (Overton Institute)

Joey Mannarino — comentarista político americano de direita, cofundador do think tank "Overton Institute" — postou uma série de mensagens no X chamando a eleição de "roubada" e pedindo intervenção internacional/americana, e compartilhou um relatório (não de órgão oficial) alegando que seções fechando tarde + votos sem biometria favoreceram Lula — as mesmas duas alegações já checadas nas seções 1-4 deste documento.

**Não conseguimos localizar o PDF/documento original** do Overton Institute, só cobertura secundária (Fox News e agregadores). O número que esse relatório cita para "votos após as 17h" é 34.393 — que, checando, é exatamente o **número oficial final do TSE** (seção 1, nota acima), não um número próprio do relatório.

**Conclusão: não acrescenta nada que já não tivesse sido coberto, e o próprio número citado já refuta a alegação.** Mesmo usando o número oficial do TSE (34.393 votos, a versão mais estrita e precisa possível da alegação), a diferença pra margem de 4,43 milhões é de ordens de magnitude — muito mais contundente que nossa própria checagem por seção inteira (seção 1), que já era generosa com a hipótese. Não há evidência nova nesse relatório que mude qualquer conclusão deste documento.

## 13. Limite de eleitores por seção (400) — terceira peculiaridade do RJ

Investigação separada das três alegações virais, mas que aprofunda o padrão de anomalias do RJ (seções 1 e 9): a legislação eleitoral fixa um teto administrativo de **400 eleitores aptos por seção** no momento em que ela é criada/redistritada (não é um limite técnico da urna, que suporta volume bem maior).

Nos dados reais (`QT_APTOS`, excluindo exterior), esse teto não é rígido no dia da eleição — seções crescem organicamente entre ciclos de redistritamento:

| Faixa de `QT_APTOS` | Seções (sem agregação) |
|---|---:|
| ≤400 | 465.315 |
| 401–420 | 8.421 |
| 421–450 | 7.058 |
| 451–500 | 2.078 |
| >500 | 99 |

Total: **17.656 seções (3,5% do Brasil, excl. exterior)** passam de 400 eleitores sem nenhuma agregação de contingência envolvida (máximo observado: 534). Isso é esperado — gap normal entre cadastro de novos eleitores e a próxima reestruturação de zona pelo TSE.

**Concentração por UF (seções >400 sem agregação):**

| UF | Acima de 400 | Total UF | % |
|---|---:|---:|---:|
| RJ | 7.570 | 37.675 | **20,09%** |
| AL | 724 | 7.101 | 10,20% |
| BA | 3.456 | 35.476 | 9,74% |
| RN | 741 | 8.115 | 9,13% |
| AM | 450 | 8.157 | 5,52% |
| MG | 2.800 | 52.062 | 5,38% |
| SP | 370 | 103.656 | 0,36% |

**RJ é destacadamente o maior outlier — 1 em cada 5 seções do estado passa de 400 eleitores, mais que o dobro do segundo colocado (AL, 10,20%) e quase 6x a média nacional (3,5%).** Esta é a **terceira anomalia distinta do RJ** nesta análise, junto com a concentração de prorrogação (seção 1: RJ lidera com 3.312-4.036 seções, dependendo do método) e a concentração de agregação na Região Metropolitana (seção 9: Itaboraí 45,74%, Petrópolis 37,56%).

**Não investigado:** se as três anomalias do RJ (seções grandes, prorrogação, agregação) têm uma causa comum (ex. RJ atrasado em processos de redistritamento/remanejamento de zonas eleitorais, crescimento populacional desigual, ou características próprias da malha eleitoral do estado) ou são três fenômenos independentes que coincidem por ser o mesmo estado. Vale uma investigação dedicada ao RJ se o usuário quiser aprofundar.

### 13.1 Teste adicional: as 99 seções >500 eleitores invertem o padrão?

Isolando as 99 seções no extremo da distribuição (`QT_APTOS` > 500, sem agregação, sem exterior — 50.795 eleitores aptos, 39.431 votos/comparecimento total; a maior em votos é Campina Grande/PB zona 17 seção 358, com 478 votos):

| | Lula | Flávio | Nulo | Branco |
|---|---:|---:|---:|---:|
| Nacional (referência) | 50,11% | 46,65% | 2,12% | 1,12% |
| Só as 99 seções >500 eleitores | 46,45% | **50,29%** | 2,22% | 1,04% |

Primeira vez num recorte desta análise em que o resultado pende pra Flávio em vez de Lula (margem Flávio +1.512 votos dentro do recorte — irrelevante em escala nacional).

**Controle por composição geográfica:** 85 das 99 seções são do município do Rio de Janeiro (capital) — a amostra é dominada por um único município, não distribuída pelo país. Teste mais rigoroso: comparar cada seção grande contra a média da própria zona eleitoral (mesmo método de z-score da seção 5), em vez da média nacional, excluindo a própria seção do cálculo da média da zona.

Resultado: **55 das 99 pendem mais pra Lula que a própria zona, 44 pendem mais pra Flávio** (delta médio +1,29pp pra Lula, mediana +0,55pp) — quase um empate, sem direção clara.

**Conclusão: a inversão contra a média nacional é um artefato de composição geográfica (concentração na capital do RJ, que já pende mais pra Flávio que a média nacional), não um efeito real de "seção grande vota diferente".** Controlando por zona eleitoral, essas 99 seções não têm um padrão direcional robusto — ao contrário das três alegações virais testadas acima, que mantinham o viés pró-Lula mesmo em testes mais rigorosos.

## 14. Tamanho de seção × prorrogação/agregação — e a reversão do padrão dentro do RJ

Testando se seções grandes (`QT_APTOS` > 400, seção 13) têm correlação com prorrogação ou agregação:

| Grupo | N seções | % Prorrogada | % Agregada |
|---|---:|---:|---:|
| ≤400 eleitores | 479.090 | 2,25% | 2,88% |
| >400 eleitores | 18.807 | **13,79%** | 6,12% |

Correlação `aptos × prorrogação` = 0,08 (fraca, mas real — mais forte que qualquer correlação causal testada na seção 7). Correlação `aptos × agregação` = 0,017 (quase nula). **Seções grandes prorrogam ~6x mais que as normais** — mecanicamente plausível: mais eleitores pra processar na mesma janela de ~9h de votação.

**Controle por UF — o efeito de tamanho é nacional, mas não explica o excesso do RJ:**

| | ≤400 eleitores | >400 eleitores | Razão grande/normal |
|---|---:|---:|---:|
| Brasil (sem RJ) | 1,91% | 6,68% | 3,5x |
| **RJ** | **7,29%** | **23,99%** | 3,6x |

A razão grande/normal é quase idêntica dentro e fora do RJ (efeito de tamanho é real e generalizável). Mas RJ já prorroga ~4x mais que o resto do país **mesmo nas seções normais** — excesso próprio, não explicado por composição de tamanho. Tamanho de seção **contribui** pra concentração de prorrogação no RJ (o estado tem 20,51% de seções grandes vs ~10% nos outros estados do topo), mas não é a causa única — segue aberta a hipótese não testada da seção 7 (velocidade de repasse da ordem judicial por região).

**Resultado eleitoral nesses recortes (Presidente):**

| Recorte | N seções | Lula | Flávio | Margem |
|---|---:|---:|---:|---:|
| Nacional (referência) | 499.207 | 50,11% | 46,65% | Lula +4.430.191 |
| RJ, grande (>400) e prorrogada | 1.854 | 44,21% | **52,27%** | **Flávio +50.776** |
| **RJ, todas as prorrogadas** | 4.036 | 45,54% | **51,01%** | **Flávio +64.587** (602.791×538.204, de 1.181.787 votos) |

**Achado inicial: dentro do RJ, prorrogação pende pra Flávio em termos absolutos — o oposto do padrão nacional (seção 2, Lula +509.302 nas seções prorrogadas do Brasil todo).** Isso sugeria que a correlação "prorrogação favorece Lula" encontrada na média nacional é puramente composicional por UF. A seção 14.1 abaixo refina essa conclusão com o teste correto (contra a média da própria UF, não a nacional).

### 14.1 Correção/refinamento: contra a própria UF, o padrão não se inverte

Teste mais rigoroso: comparar prorrogação contra a média **da própria UF** (não a nacional), pra separar "efeito real da prorrogação" de "o estado já vota assim de qualquer forma". Testado em RJ (pende Flávio) e BA (pende Lula) como contraponto — as duas UFs mais opostas política e demograficamente entre as que concentram prorrogação.

| UF | Geral (toda a UF) | Só seções prorrogadas | Delta pró-Lula |
|---|---:|---:|---:|
| RJ (4.036 seções prorrogadas) | Lula 45,30% / Flávio 51,29% | Lula 45,54% / Flávio 51,01% | **+0,24pp** |
| BA (2.108 seções prorrogadas) | Lula 68,83% / Flávio 27,69% | Lula 70,08% / Flávio 26,03% | **+1,25pp** |

**Resultado: o padrão não se inverte no RJ quando controlado pela própria UF — ele só fica pequeno demais pra enxergar na média nacional.** A prorrogação desvia pra Lula tanto no RJ (+0,24pp) quanto na BA (+1,25pp) — mesma direção nas duas UFs, ambas com magnitude pequena. O que parecia uma "reversão" no RJ (seção 14, resultado bruto Flávio +64.587) era um artefato de comparar contra a média *nacional*: o RJ já pende pra Flávio como um todo, então qualquer recorte seu (prorrogado ou não) também pende pra Flávio em termos absolutos — não é a prorrogação mudando de direção, é o RJ sendo o RJ.

**Conclusão mais precisa, substitui a da seção 14:** dentro de cada UF, prorrogação tem um viés real mas pequeno a favor de Lula (mais forte na BA que no RJ, mas ambos leves — nada perto de decidir a eleição). O achado de que RJ prorrogada pende Flávio *em termos absolutos* continua correto e vale manter (é real, está nos dados), mas a explicação é composicional pura (RJ já é um estado de Flávio), não uma reversão do mecanismo de prorrogação em si.

## 15. Eleição nacional isolando as seções grandes (>400)

Isolando as 18.807 seções com `QT_APTOS` > 400 no Brasil (excl. exterior, mesmo grupo "is_grande" da seção 14):

| | Lula | Flávio | Nulo | Branco |
|---|---:|---:|---:|---:|
| Nacional (referência) | 50,11% | 46,65% | 2,12% | 1,12% |
| Seções grandes (>400, Brasil) | **52,43%** | 44,06% | 2,40% | 1,11% |

Margem gerada dentro do recorte: **+540.297** (3.385.607 × 2.845.310, de 6.457.629 votos) — **12,2% da margem nacional** (+4.430.191). Excluindo essas 18.807 seções inteiramente do total nacional: Lula ainda **+3.889.894**.

**Nota importante sobre magnitude:** esse recorte tem 6,46M votos — maior que a própria margem nacional (4,43M). Isso significa que, em tese, o grupo tem massa suficiente para ter decidido a eleição *se tivesse se desviado o bastante* do padrão nacional (um desvio de ~35pp bastaria). Mas o desvio real medido é de apenas 12,2% da margem — o teste que importa não é o tamanho do grupo, é o quanto ele de fato se desviou, e esse desvio é modesto. **Conclusão: real e mensurável, mas não decisivo** — mesma lógica de prorrogação/agregação/biometria (seções 1, 3, 4), que também geram margens somadas sem decidir o resultado.

**Consistente com a seção 14.1:** o viés pró-Lula nacional de seções grandes é a soma de vieses pequenos e específicos por UF — RJ isoladamente pende Flávio nesse recorte (visto nas 99 seções >500, seção 13.1), mas a maioria absoluta das seções grandes do país está em outros estados (MG, BA, AL — seção 13) que pendem mais pra Lula, dominando a média nacional.

## 16. Eleição nacional isolando as seções pequenas (<195)

Corte escolhido por simetria estatística com a seção 15 (mesmo N aproximado: 19.215 seções pequenas vs 18.807 grandes). Isolando as 19.215 seções com `QT_APTOS` < 195 no Brasil (excl. exterior):

| | Lula | Flávio | Nulo | Branco |
|---|---:|---:|---:|---:|
| Nacional (referência) | 50,11% | 46,65% | 2,12% | 1,12% |
| Seções pequenas (<195, Brasil) | **57,75%** | 39,41% | 1,85% | 0,99% |

**O viés pró-Lula mais forte de qualquer recorte testado nesta análise** — maior que o decil mais extremo de biometria (53,27%, seção 4) e maior que seções grandes (52,43%, seção 15).

**Teste de margem + exclusão:**
- Margem gerada dentro do recorte: **+424.213** (1.335.829 × 911.616, de 2.313.175 votos) — 9,58% da margem nacional (+4.430.191).
- Excluindo essas 19.215 seções inteiramente do total nacional: Lula ainda **+4.005.978**.
- Massa do grupo (2,31M votos) é **menor** que a margem nacional (4,43M) — diferente do grupo "grande" (seção 15, 6,46M), esse grupo nem teoricamente teria massa suficiente pra inverter a eleição por conta própria, mesmo em desvio extremo.

**Concentração por UF — explicação provável é geografia, não um mecanismo de "seção pequena":**

| UF | Seções pequenas (<195) | Total UF | % |
|---|---:|---:|---:|
| PI | 1.106 | 10.225 | **10,82%** |
| MA | 1.569 | 18.093 | 8,67% |
| PA | 1.616 | 20.827 | 7,76% |
| RS | 1.886 | 27.547 | 6,85% |
| CE | 1.550 | 23.765 | 6,52% |
| MG | 2.467 | 52.062 | 4,74% (maior N absoluto) |
| BA | 1.596 | 35.476 | 4,50% |
| RJ | 843 | 37.675 | 2,24% (menor do top 10) |

PI e RS lideram a concentração proporcional — os mesmos dois estados que lideravam a concentração de falha de biometria (seção 4: PI 26,15%, RS 20,43%). Provável fator comum: geografia rural/remota (não uma causalidade direta entre as duas coisas, ambas são proxies de "interior"). RJ, estado majoritariamente urbano, tem a menor proporção do top 10 (2,24%) — consistente com o perfil oposto já visto na seção 13 (RJ concentra seções *grandes*, não pequenas).

**Conclusão: viés pró-Lula forte mas explicado por composição geográfica (concentração Norte/Nordeste/interior, região historicamente mais forte pro PT), não um mecanismo causal de "seção pequena".** Mesmo padrão de raciocínio das seções 13-15: tamanho de seção correlaciona com geografia, que correlaciona com voto — não um efeito direto de tamanho sobre o resultado.

## 17. Concentração de votos em branco/nulo

Mesmo método da biometria (seção 4): taxa de branco+nulo por seção, decis, correlação com tamanho de seção.

**Escala nacional:** 4.144.009 votos branco+nulo (3,24% do total) — bem menor que a escala de biometria (6,8%).

| Decil (taxa branco+nulo) | % Lula (entre válidos) |
|---|---:|
| 1 (menor, 0–1,40%) | 52,75% |
| 5 (2,70–3,07%) | 51,41% (mínimo) |
| 10 (maior, 5,19–28,23%) | 52,55% |

Referência nacional (válidos): 51,78%. **Sem padrão direcional claro** — formato em U entre os decis, não uma inclinação constante como biometria (onde decil 1→10 subia monotonicamente). Tanto o decil mais baixo quanto o mais alto pendem levemente pra Lula; os decis do meio pendem levemente pra Flávio.

**Margem + exclusão (decil 10, maior taxa):** margem gerada +606.446; excluindo inteiramente, Lula ainda +3.823.745 — real mas modesto, mesma ordem dos outros mecanismos.

**Diferente da biometria, não é um efeito de tamanho de seção:** correlação `aptos × taxa_bn` = **+0,105** (oposto da biometria, que foi -0,17). Seções pequenas (<195) têm taxa de branco/nulo **mais baixa** (2,83%) que o resto (3,23%), não mais alta. O viés de tamanho de seção achado na biometria não generaliza pra branco/nulo — são fenômenos independentes, respondendo a causas diferentes.

**Os extremos são ruído de amostra pequena, não uma categoria identificável:** top 20 seções por taxa são municípios pequenos/remotos (Ipixuna/AM, Assis Brasil/AC, Tarauacá/AC, Barcelos/AM aparece 3x), todos com comparecimento de 28-250 votos — com N tão baixo, poucos nulos bastam pra gerar taxas de 15-28%. Diferente dos outliers de % Lula (seção 5, presídios/aldeias indígenas bem definidos), aqui não há padrão institucional identificável.

**Conclusão: o teste com resultado mais nulo desta análise inteira.** Branco/nulo não decide a eleição (margem pequena, exclusão não muda o resultado) nem é um fenômeno sistemático ligado às outras anomalias já encontradas (RJ, tamanho de seção, biometria) — é, na melhor hipótese, ruído estatístico de seções pequenas N, sem direção política.

### 17.1 Teste adicional: branco/nulo explica as "ilhas lulistas" (seção 8)?

Mesmo teste da seção 8 (biometria), agora com taxa de branco/nulo: seções com z-score fortemente positivo (`z≥3`, bem mais Lula que a zona) dentro de zonas onde a média já pende pro Flávio (`media_zona<50%`).

| Grupo | N seções | % Lula médio | Taxa de branco/nulo |
|---|---:|---:|---:|
| Resto da zona bolsonarista | 246.749 | 39,26% | 3,25% (3,27% ponderada) |
| Ilha lulista (z≥3) | 1.233 | 66,01% | 3,24% (3,26% ponderada) |

**Conclusão: nulo, igual à biometria.** As ilhas lulistas não têm taxa de branco/nulo diferente do resto da zona bolsonarista. Fecha mais uma hipótese operacional possível — nem biometria nem branco/nulo explicam esses bolsões de Lula em território de Flávio; o fenômeno parece genuinamente político/demográfico, não um artefato detectável nesses canais.

## 18. Comparação com pesquisas de intenção de voto (fora do escopo de dados do TSE)

**Aviso de escopo específico desta seção:** diferente de todo o resto do documento, esta seção usa **pesquisas eleitorais** (Datafolha, Quaest, MDA — dados de terceiros, não do TSE), buscadas via web em cobertura jornalística, não baixadas como dado aberto estruturado. Pesquisa de intenção de voto é uma **previsão feita antes da eleição** (amostragem, modelagem de comparecimento) — estatisticamente independente da apuração do TSE (que **conta** o voto depois do fato). Erro de pesquisa não é evidência de problema na apuração; é só desempenho preditivo do instituto. Mantido aqui porque ilumina (sem comprovar) possíveis explicações para a dinâmica do 2º turno já discutida nas seções 6, 6.1 e 6.2.

### 18.1 Pesquisas de véspera do 2º turno (24/10) vs. resultado real

| Pesquisa | Lula (válidos) | Flávio (válidos) |
|---|---:|---:|
| Datafolha (24/10) | 49% | 51% |
| Quaest (24/10) | 49% | 51% |
| **Resultado oficial (TSE, válidos)** | **51,79%** | **48,21%** |

As duas pesquisas finais convergiam em Flávio +2pp (dentro da margem de erro). O resultado real foi Lula +3,58pp — swing de **~5,8pp** em relação à projeção, no sentido oposto. Erro concentrado em **subestimar Lula**.

### 18.2 Pesquisas de véspera do 1º turno (03/10) vs. resultado real

Resultado real do 1º turno (válidos, todos os candidatos): Lula 45,16% × Flávio 47,02% (calculado a partir de `votacao_secao_2026_BR.csv`, seção 6.1).

| Instituto | Lula previsto | Erro Lula | Flávio previsto | Erro Flávio |
|---|---:|---:|---:|---:|
| Datafolha | 45,0% | +0,16pp | 42,0% | **+5,02pp** |
| Quaest | 46,0% | -0,84pp | 45,0% | **+2,02pp** |
| MDA | 47,8% | -2,64pp | 42,1% | **+4,92pp** |

Erro concentrado em **subestimar Flávio** — padrão oposto ao do 2º turno.

**Achado principal: o erro troca de lado entre os turnos.** No 1º turno, as pesquisas pegaram bem o Lula e erraram pra baixo no Flávio; no 2º turno, o oposto — erraram pra baixo no Lula. Não é um viés simples e unidirecional ("pesquisa sempre favorece X").

**Ressalva importante — isto é uma hipótese explicativa, não uma conclusão, e não elimina a hipótese de manipulação.** Uma explicação possível e consistente com o padrão é dinâmica diferente em cada momento — possível voto subdeclarado pró-Flávio no 1º turno (padrão já documentado em eleições anteriores com candidatos bolsonaristas no Brasil, 2018/2022) e consolidação de última hora de eleitores de candidatos eliminados pendendo mais pra Lula no 2º turno do que as pesquisas, feitas com 1-3 semanas de antecedência, conseguiram captar. Mas essa é só uma das explicações compatíveis com o dado — um erro de pesquisa desse tamanho, por si só, **não distingue estatisticamente** entre "o eleitorado real se moveu assim e a pesquisa não captou" e "o resultado apurado foi alterado em relação ao que o eleitorado real votou". Pesquisa de intenção de voto não é uma medição independente da apuração que possa confirmar ou refutar fraude — é só outra estimativa do mesmo evento, sujeita a seus próprios erros (amostragem, modelagem de comparecimento, timing). Esta seção ilumina um padrão interessante; não fecha, nem endossa, a questão de integridade da apuração, que está fora do que qualquer dado usado neste documento consegue verificar (ver "Aviso sobre o escopo").

### 18.3 Pesquisa regional do Nordeste: o "boom" de Lula já aparecia?

Único recorte regional confiável encontrado (Datafolha; não foi possível confirmar um cruzamento Nordeste-específico da Quaest comparável pra este confronto Lula×Flávio no período):

| Data | Lula (NE) | Flávio (NE) |
|---|---:|---:|
| Datafolha 02/10 | 62% | 31% |
| Datafolha 08/10 | 61% | 35% |
| **Resultado real, 2º turno (NE)** | **69,37%** | **30,63%** |

**O sinal de "Nordeste pró-Lula" já existia há semanas, mas a tendência visível na pesquisa ia no sentido contrário ao que aconteceu depois:** entre 02/10 e 08/10, a pesquisa mostrava Flávio ganhando terreno no Nordeste (31%→35%), não Lula. O resultado final inverteu isso por completo — Lula terminou mais alto que qualquer leitura de pesquisa, Flávio mais baixo. Uma leitura possível é que a consolidação regional pró-Lula se concentrou na reta final (depois de 08/10, sem dado de pesquisa pra confirmar o momento exato), consistente com o mecanismo de transferência de voto de candidatos eliminados já identificado nas seções 6.1/6.2 — mas, pela mesma ressalva da seção 18.2, essa é uma hipótese compatível com o dado, não a única, e o dado de pesquisa não serve pra confirmar ou descartar manipulação na apuração.

## 19. Seções com resultado numericamente idêntico (tupla Lula/Flávio/Branco/Nulo repetida)

Teste de forense eleitoral clássico: existem seções com a contagem exata de votos (Lula, Flávio, Branco, Nulo) repetida em outra seção? Repetição exata entre urnas distantes geograficamente é um padrão associado, em outras eleições/países, a cópia de boletim — mas também é o resultado esperado de pura coincidência combinatória quando o espaço de resultados plausíveis é pequeno (seções de porte baixo/médio, eleição apertada).

**Nacional: 117.752 das 499.205 seções ativas (23,59%) têm a tupla completa (Lula, Flávio, Branco, Nulo) idêntica à de pelo menos outra seção.**

- **Não se concentra nas seções maiores — pelo contrário.** Taxa de duplicação por faixa de comparecimento: <50 eleitores 14,68%; 150-199: 21,66%; 200-249: 27,45%; 250-299: 28,31% (pico, é a faixa com mais seções); 300-349: 14,52%; 350+: só 1,96%. Se fosse cópia deliberada, esperaríamos concentração nas seções grandes (mais "lucrativo" fraudar); o padrão observado é o oposto — concentra onde há mais seções competindo pelo mesmo espaço pequeno de combinações possíveis (efeito "paradoxo do aniversário").
- **87,6% dos pares/grupos duplicados estão espalhados entre UFs diferentes** (103.126 de 117.752 seções) — só 286 seções duplicam dentro da própria zona eleitoral. Cópia local de boletim produziria o padrão inverso (concentração geográfica); o observado é dispersão nacional, assinatura de coincidência estatística.
- **Verificação do espaço combinatório:** para comparecimento=250, por exemplo, só 2.981 tuplas distintas aparecem de fato entre 3.564 seções — bem menos que o espaço teórico de combinações possíveis (~2,67 milhões). O voto real se concentra numa faixa estreita de proporções (eleição nacional apertada + seções com perfil demográfico parecido dentro da mesma região), o que por si só já explica a taxa alta de coincidência.
- **Margem dentro das seções duplicadas: -14.141 votos (Flávio), 49,98% Lula** — essencialmente um empate técnico, sem direção partidária. Teste de margem + exclusão: isolando só as seções duplicadas, Flávio venceria por margem mínima; excluindo-as do total nacional, Lula mantém +4.444.332 (praticamente igual à margem oficial de +4.430.191). Duplicata não decide nem distorce o resultado.
- **Por UF, maior incidência:** DF (32,69%), MS (31,73%), GO (30,82%), AP (29,10%), PR (28,65%) — sem padrão político claro (não favorece um lado), e RJ fica em posição intermediária (8º lugar), diferente de outras anomalias desta análise onde RJ se destacava.

### 19.1 Caso aprofundado: Distrito Federal

DF é a UF com maior taxa de duplicação (32,7%, 2.278 das 6.969 seções). Investigado em detalhe porque é o maior outlier nacional:

| Recorte | Lula | Flávio | Margem | % Lula |
|---|---:|---:|---:|---:|
| DF geral (real, todas as seções) | 825.224 | 979.542 | -154.318 (Flávio) | 45,72% |
| DF só seções duplicadas (2.278) | 267.263 | 320.657 | -53.394 (Flávio) | 45,46% |
| DF excluindo as duplicadas | 557.961 | 658.885 | -100.924 (Flávio) | 45,85% |

Proporção idêntica à do DF como um todo — sem distorção.

**Mas quase todas essas 2.278 seções do DF duplicam com seções de *outros* estados, não entre si:** só **125 seções (1,79% do DF)** repetem a tupla com *outra seção do próprio DF* (62 grupos — 61 pares + 1 trio). Isolando só essas 125: Lula 14.596 × Flávio 17.406 (45,61%, margem -2.810). Excluindo-as do DF: 45,73%. De novo, nenhuma distorção — mesma proporção Flávio-favorável do DF inteiro.

### 19.2 As seções duplicadas têm falha de biometria diferente do normal?

Último teste cruzado: se a duplicação de resultado estivesse ligada a algum tipo de manipulação por bypass de identificação, esperaríamos taxa de falha biométrica fora do padrão nessas seções.

| Recorte | Taxa de falha biométrica |
|---|---:|
| Nacional | 6,732% |
| Seções duplicadas (ponderada, 117.752 seções) | 6,735% |
| Seções duplicadas (média simples por seção) | 6,843% |
| Resto (não-duplicadas) | 6,731% |

Diferença de 0,003 a 0,11 ponto percentual — ruído, não sinal. As seções com tupla duplicada não têm nenhum traço distintivo de biometria em relação ao resto do país.

**Conclusão da seção 19:** a duplicação exata de resultado é real e numericamente expressiva (23,6% das seções), mas todas as evidências (dispersão geográfica nacional, concentração no porte médio não no grande, ausência de viés partidário, taxa de biometria idêntica à nacional, espaço combinatório pequeno pra eleição apertada) apontam para coincidência estatística, não cópia de boletim entre urnas nem bypass de identificação.

## 20. Seções com zero votos em um dos dois candidatos

Teste complementar: existe alguma seção ativa (comparecimento > 0) onde um dos dois candidatos recebeu **zero** votos?

**Nacional: 4 seções com Lula=0 (0,001%) e 106 seções com Flávio=0 (0,021%)** — sobre 499.205 seções ativas. Assimetria de ~26x entre os dois lados, mas ambos os grupos são eventos raríssimos em termos absolutos.

**As 4 seções com Lula=0** (pequenas, 49-118 eleitores):

| UF | Município | Comparecimento | Flávio |
|---|---|---:|---:|
| RS | Arroio do Padre | 118 | 116 |
| RS | Cândido Godói | 85 | 82 |
| PA | Jacareacanga | 50 | 50 |
| PA | Chaves | 49 | 48 |

Arroio do Padre e Cândido Godói são as mesmas cidades de colonização alemã do RS já identificadas na seção 5.1 como os municípios mais pró-Flávio da análise — bate com achado anterior, reforça em vez de contradizer.

**As 106 seções com Flávio=0** concentram em **AM (30), MA (20), PA (16)** — Norte/Nordeste — com comparecimento bem maior que o lado Lula=0 (até 354 eleitores, todos votando Lula). Top 5 por comparecimento:

| UF | Município | Comparecimento | Lula |
|---|---|---:|---:|
| AM | Tabatinga | 365 | 354 |
| AM | Tabatinga | 347 | 338 |
| AM | Maués | 337 | 333 |
| AM | Atalaia do Norte | 335 | 325 |
| AM | Santo Antônio do Içá | 330 | 329 |

Tabatinga, Atalaia do Norte e Santo Antônio do Içá são exatamente os municípios de fronteira do Amazonas já identificados na seção 5.1 como os mais extremamente pró-Lula do país.

**Conclusão da seção 20:** os dois extremos (zero-Lula no RS, zero-Flávio no AM/MA/PA) batem com os padrões geográficos de homogeneidade política/demográfica já documentados na seção 5.1 (colonização alemã pró-Flávio × fronteira amazônica indígena/ribeirinha pró-Lula). São seções pequenas, de comunidades isoladas e politicamente/demograficamente homogêneas — consistente com o que já se sabia da geografia eleitoral local, não um padrão novo ou evidência de manipulação.

### 20.1 O padrão já existia no 1º turno?

Cruzamos as 110 seções zeradas do 2º turno (4 Lula=0 + 106 Flávio=0) com o comportamento da *mesma seção/urna* no 1º turno (`data/bu_web_1t/*.csv`, mesmo `SG_UF`/`NM_MUNICIPIO`/`NR_ZONA`/`NR_SECAO`), antes de qualquer dinâmica de consolidação de 2º turno.

**As 4 seções com Lula=0 no 2T já tinham Lula=0 no 1º turno também** (Arroio do Padre/RS, Cândido Godói/RS, Jacareacanga/PA, Chaves/PA) — persistência perfeita, 4 de 4.

**Das 106 seções com Flávio=0 no 2T:**
- **50 (47%) já tinham Flávio=0 no 1º turno também.**
- **99 (93%) já tinham ≤3 votos em Flávio no 1T** — praticamente zero, antes mesmo de Flávio ser o único candidato de direita na disputa.
- % Lula médio dessas seções no 1T: **95,9%** — já eram quase unânimes antes do 2º turno existir.
- Só 7 seções tiveram Flávio com alguma presença um pouco maior no 1T (4 a 16 votos, ainda ínfimo sobre comparecimentos de 100-330) — nenhuma representa uma queda de "presença real" pra zero entre os turnos.

**Conclusão da seção 20.1: o shutout não é um evento do 2º turno.** É uma característica estrutural e estável dessas comunidades, já presente no 1º turno — mesma lógica da persistência de biometria 1T×2T (seção 4.0). Reforça que não há ligação com logística/apuração específica do 2º turno.

## Conclusão geral

Nenhuma das três alegações, isolada ou somada, tem escala suficiente pra ter decidido o 2º turno:

| Mecanismo | Margem gerada a favor de Lula | Margem nacional se excluído inteiramente |
|---|---:|---:|
| Prorrogação | +509.302 | +3.920.889 |
| Agregação/troca de urna (sem exterior, contingência real) | +826.618 | +3.576.732* |
| Decil mais alto de falha de biometria | +1.111.551 | +3.318.640 |
| **Margem oficial nacional** | | **+4.430.191** |

*valor da exclusão total calculado antes da correção do exterior (seção 10); a diferença é da ordem de dezenas de milhares de votos, não muda a conclusão.

Os três mecanismos pendem na mesma direção (a favor de Lula, relativo à média nacional), mas nenhum deles — nem a soma dos três — chega perto de inverter o resultado. O padrão merece nota como achado legítimo (não é ruído), mas não sustenta a alegação de que teria mudado o vencedor.

**Ressalva que vale destacar sobre biometria especificamente:** dos três, é o único mecanismo que envolve uma redução real na verificação de identidade de quem vota (liberação manual pelo mesário, no lugar da biometria) — um vetor de risco teórico de personificação que os outros dois (prorrogação, agregação) não têm, já que esses são só sobre quando/onde o voto aconteceu. Não achamos evidência estatística de exploração desse vetor (seções 4.1, 8), mas isso é diferente de dizer que ele foi auditado e está limpo — o voto secreto impede, por desenho, cruzar quem teve falha de biometria com em quem votou. Essa é a lacuna mais honesta desta análise: não "sem risco", mas "risco que não temos como auditar com dado aberto".

**Pendente, não investigado:** se a correlação é causal (prorrogação/agregação/biometria "geram" votos pra Lula) ou puramente composicional (os lugares onde essas ocorrências acontecem já pendiam mais pra Lula por outros motivos — geografia, perfil do eleitorado). O achado de que RJ domina prorrogação e agregação, mas não biometria, sugere que são pelo menos dois fenômenos distintos, não uma causa única. O RJ acumula uma **terceira** anomalia (seção 13: 20,09% das seções do estado acima do limite de 400 eleitores, muito acima da média nacional de 3,5%) — parcialmente explicada pela seção 14 (seções grandes prorrogam mais em qualquer lugar, efeito real e nacional), mas o RJ também tem um excesso de prorrogação **independente** do tamanho de seção, ainda sem explicação. A seção 14 achou que, em termos absolutos, prorrogação no RJ pende pra Flávio (+64.587) — mas a seção 14.1 mostrou que, controlando pela própria UF (não a média nacional), o viés pró-Lula da prorrogação é pequeno mas **não se inverte** no RJ (+0,24pp) nem na BA (+1,25pp) — é a mesma direção nas duas UFs mais opostas política/demograficamente que testamos. O "efeito Lula" da prorrogação é real mas pequeno e consistente por UF; o que varia de UF pra UF é só o patamar de onde ele parte.
