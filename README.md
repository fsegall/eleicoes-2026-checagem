# PollReview — Eleições Gerais 2026 (Brasil)

Análise de dados abertos do TSE sobre o 1º e 2º turno das eleições gerais de 2026, com foco em checar — com dados, de forma reprodutível — as principais alegações que circularam sobre o 2º turno da eleição presidencial (Lula × Flávio Bolsonaro).

## Aviso importante sobre o escopo desta análise

Esta é uma análise estatística dos **dados abertos exatamente como o TSE os publicou** — nada além disso. Ela testa a consistência interna desses dados (os números batem entre si? os padrões encontrados fazem sentido geográfico/demográfico? alguma alegação específica de fraude é compatível com o que os números mostram?). Ela **não** é, e não pretende ser:

- Uma atestação de que os dados abertos divulgados pelo TSE não foram alterados, de alguma forma, antes da divulgação;
- Uma atestação de que a logística da eleição (preparação de urnas, transmissão, apuração) ocorreu sem falha, fraude ou irregularidade fora do que é capturado nesses dados;
- Uma certificação de lisura do processo eleitoral como um todo.

A ausência de anomalia estatística nos dados divulgados não resolve a pergunta de se esses dados são, eles mesmos, um retrato fiel do que aconteceu nas urnas — essa é uma pergunta sobre a qual dado aberto, por definição, não tem como se pronunciar, porque ela está logicamente a montante do próprio dado. Não estamos afirmando que tudo funcionou perfeitamente bem; estamos afirmando que, **dentro do que os dados abertos disponibilizados permitem verificar**, as alegações específicas testadas abaixo não se sustentam.

## O que este projeto responde

Três alegações virais sobre o 2º turno foram testadas ponto a ponto:

1. **Prorrogação do horário de votação** teria decidido a eleição?
2. **Urnas trocadas / seções agregadas** (contingência operacional) teriam decidido?
3. **Votos sem confirmação biométrica** teriam decidido?

Além disso, o projeto investigou um conjunto de anomalias operacionais encontradas pelo caminho — concentração de ocorrências no Rio de Janeiro, o limite administrativo de eleitores por seção, o padrão geográfico do voto no exterior, falha de biometria entre o 1º e o 2º turno, e mais.

**Resultado resumido:** nenhuma das três alegações, isolada ou somada, tem escala suficiente para ter decidido o resultado. Cada uma gera um viés real e mensurável a favor de Lula em relação à média nacional, mas a margem oficial (+4.430.191 votos) resiste a excluir qualquer um dos grupos por completo. O detalhamento completo — com todas as tabelas, métodos e ressalvas — está em [`docs/checagem_alegacoes_virais_2t.md`](docs/checagem_alegacoes_virais_2t.md).

## Estrutura do repositório

```
├── notebooks/
│   └── 01_eleicoes_2026_presidente.ipynb   # Notebook principal: exploração + relatório visual
├── docs/
│   ├── checagem_alegacoes_virais_2t.md     # Documento principal: checagem das 3 alegações + achados
│   ├── tse_dados_abertos.md                # Dicionário de campos e notas técnicas dos datasets do TSE
│   └── achados_2t_ocorrencias_urna.md      # Classificação inicial de ocorrências (prorrogação/contingência)
├── data/                                    # Dados brutos do TSE — não versionados, ver "Dados" abaixo
└── CLAUDE.md                                # Notas de ambiente e contexto do projeto
```

## Dados

Os dados usados são todos **dados abertos do TSE** (`dadosabertos.tse.jus.br`), baixados via a CDN oficial (`cdn.tse.jus.br`). Não estão neste repositório porque são grandes demais pra versionar em Git (a pasta `data/` chega a ~32GB com os três datasets completos):

| Dataset | O que é | Tamanho aprox. |
|---|---|---:|
| `votacao_secao_2026_BR.csv` | Votação por seção eleitoral, nacional, Presidente, 1º e 2º turno | 1,5GB |
| `bu_web_2t/*.csv` | Boletim de Urna Web, 2º turno (Presidente + Governador) — tem horário de abertura/encerramento e sinais de contingência de urna | 880MB |
| `bu_web_1t/*.csv` | Boletim de Urna Web, 1º turno (todos os cargos) | ~30GB |

Para reproduzir a análise, baixe os datasets pela [API do TSE Dados Abertos](https://dadosabertos.tse.jus.br) (endpoint `package_show`, datasets `resultados-2026` e `resultados-2026-boletim-de-urna`) e extraia em `data/` seguindo a estrutura acima. Detalhes de schema, convenções de encoding (`latin1`, delimitador `;`) e os dois mecanismos de contingência de urna estão documentados em [`docs/tse_dados_abertos.md`](docs/tse_dados_abertos.md).

## Como rodar

```bash
python3 -m venv venv
source venv/bin/activate
pip install duckdb pandas jupyter ipykernel matplotlib seaborn numpy
python -m ipykernel install --user --name=pollreview
jupyter notebook notebooks/01_eleicoes_2026_presidente.ipynb
```

O motor de consulta é o [DuckDB](https://duckdb.org/), que lê os CSVs diretamente do disco sem carregar tudo em memória — importante dado o volume de dados do 1º turno (~30GB).

## Metodologia

Toda classificação usa fuso horário oficial por UF/município (não aproximação estatística), com as exceções legais de fuso (AC, parte do AM, MT, MS, RO, RR) tratadas explicitamente. Testes estatísticos incluem decis de taxa por seção, z-score dentro da própria zona eleitoral para detecção de outliers, e testes de exclusão (recalcular o resultado nacional removendo cada grupo por completo) para separar "viés direcional real" de "margem suficiente para decidir". A seção "Ressalvas epistemológicas" de [`docs/checagem_alegacoes_virais_2t.md`](docs/checagem_alegacoes_virais_2t.md) é explícita sobre os limites do que dado aberto agregado consegue (e não consegue) verificar — em particular, o sigilo do voto impede isolar como votaram especificamente os eleitores sem confirmação biométrica.

## Autoria

**Livres Soluções de Software, Informação e Pesquisa**

Análise gerada em parceria com o Claude (Anthropic).
