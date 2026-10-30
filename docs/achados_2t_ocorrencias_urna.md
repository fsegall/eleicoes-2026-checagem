# Achados — ocorrências operacionais no 2º turno (2026)

Análise feita em `notebooks/01_eleicoes_2026_presidente.ipynb`, seção "2º turno — ocorrências operacionais". Fonte: Boletim de Urna Web (`data/bu_web_2t/*.csv`). Dicionário de campos completo em `tse_dados_abertos.md`.

## Metodologia

Classificação por seção eleitoral (`TIPO_OCORRENCIA_SECAO`), combinando três sinais, do mais específico pro mais genérico:

1. **Agregada (contingência)** — `DS_SECOES_AGREGADAS` preenchido: a seção não conseguiu votar na própria urna e foi atendida por outra seção já funcionando. Sinal direto, confirmado nos dados.
2. **Prorrogada** — `DT_ENCERRAMENTO` mais de 30 minutos depois da mediana de encerramento daquela UF. Detecção estatística (desvio da mediana por UF), não uma tabela fixa de fuso horário — evita o problema dos ~11 municípios do sudoeste do Amazonas que seguem o fuso do Acre.
3. **Abertura atípica (possível contingência)** — `DT_ABERTURA` mais de 30 minutos depois da mediana de abertura daquela UF. Sinal indireto e fraco: pode ser urna de contingência ativada tarde, atraso de mesário, ou outra causa. **Não confirma troca de urna** — não existe no TSE um dataset aberto com a alocação original planejada de urna por seção pra comparar contra `NR_URNA_EFETIVADA` (investigamos o log Gedai; é sobre preparação de mídia de carga, não sobre alocação seção↔urna).

## Resultado nacional (2º turno, 2026)

| `tipo_ocorrencia_secao` | Qtd. seções |
|---|---:|
| Normal | 475.290 |
| Agregada (contingência) | 16.064 |
| Prorrogada | 7.477 |
| Abertura atípica (possível contingência) | 298 |
| Agregada e prorrogada | 78 |
| **Total** | **499.207** |

Caveat importante: o TSE reportou ~24.268 seções abertas depois das 17h10 (horário de Brasília) nacionalmente — número bem maior que nossas 7.555 (7.477+78) "prorrogadas". **Isso é esperado, não é erro de dado**: a metodologia é diferente (desvio da mediana de cada UF vs. corte absoluto único às 17h10 nacional). Se quisermos comparar de forma mais direta com o número oficial, precisaríamos de uma tabela de fuso horário por UF (ou por município, pros casos de exceção) em vez do método estatístico.

## Top UFs por prorrogação

| UF | Seções prorrogadas |
|---|---:|
| RJ | 3.312 |
| SP | 1.960 |
| BA | 1.041 |
| PE | 717 |
| PB | 316 |

**Achado a investigar:** RJ concentra 3.312 das 7.555 seções prorrogadas — desproporcional mesmo considerando que SP e BA têm mais seções no total. Hipóteses a checar: concentrado num município específico do RJ (capital? região metropolitana?), ou é um artefato da metodologia (RJ tem uma mediana de encerramento "adiantada" por algum motivo, inflando o número de outliers). Ainda não investigado.

## Top UFs por agregação de seção

| UF | Seções agregadoras |
|---|---:|
| BA | 2.198 |
| MG | 1.876 |
| CE | 1.656 |
| MA | 1.487 |
| ZZ (exterior) | 1.216 |

Nota: ZZ (voto no exterior) aparecer no top 5 é inesperado — merece checar se faz sentido (exterior tem volume de seções muito menor que estados grandes, então 1.216 seções agregadas lá é uma proporção enorme do total de seções no exterior, não investigado ainda).
