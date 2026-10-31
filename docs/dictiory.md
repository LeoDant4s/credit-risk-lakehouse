# Dicionário de dados — SCR.data (Banco Central)

Projeto de portfólio de engenharia de dados. Elaborado a partir de uma amostra de 4 linhas (`data_base` 2025-06-30). As descrições foram inferidas dos nomes das colunas e da amostra, e precisam ser confirmadas no dicionário oficial do BCB.

## Visão geral do arquivo

- Formato: CSV, 24 colunas, separador `;`, todos os campos entre aspas duplas.
- Granularidade: dados agregados. Cada linha é uma combinação única de `data_base` e das 9 dimensões, sem identificação de cliente ou contrato.
- Números: vírgula como separador decimal e sem separador de milhar (`1198363,68`).
- Datas: formato `YYYY-MM-DD`, sempre o último dia do mês.
- Camada Bronze: todas as colunas como `STRING`. A coluna "Tipo (Silver)" abaixo é o tipo-alvo após a conversão.

## Colunas

| # | Coluna | Grupo | Tipo (Silver) | Descrição | Exemplo |
| --- | --- | --- | --- | --- | --- |
| 1 | data\_base | Tempo | DATE | Data de referência da posição (fechamento do mês) | 2025-06-30 |
| 2 | uf | Dimensão | STRING(2) | Unidade da federação | AC |
| 3 | segmento | Dimensão | STRING | Segmento da instituição financeira | Banco, Arrendamento |
| 4 | cliente | Dimensão | STRING | Tipo de cliente: PF (pessoa física) ou PJ (pessoa jurídica) | PJ |
| 5 | cnae\_ocupacao | Dimensão | STRING | PJ: seção de atividade econômica (CNAE). PF: ocupação | Construção; Aposentado/pensionista |
| 6 | porte | Dimensão | STRING | PJ: porte da empresa. PF: faixa de renda em salários mínimos | Médio; Acima de 20 salários mínimos |
| 7 | modalidade | Dimensão | STRING | Modalidade da operação de crédito | Operações de arrendamento |
| 8 | submodalidade | Dimensão | STRING | Detalhamento da modalidade | Arrendamento financeiro exceto veículos automotores e imóveis |
| 9 | origem | Dimensão | STRING | Origem ou destinação dos recursos | Sem destinação específica |
| 10 | indexador | Dimensão | STRING | Indexador da operação | Prefixado |
| 11 | numero\_de\_operacoes | Volume | BIGINT | Quantidade de operações. Vale -1 nas 4 linhas da amostra (possível marcador de valor omitido) | -1 |
| 12 | a\_vencer\_ate\_90\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer em até 90 dias | 159911,55 |
| 13 | a\_vencer\_de\_91\_ate\_360\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer entre 91 e 360 dias | 420513,73 |
| 14 | a\_vencer\_de\_361\_ate\_1080\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer entre 361 e 1080 dias | 617938,40 |
| 15 | a\_vencer\_de\_1081\_ate\_1800\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer entre 1081 e 1800 dias | 0,00 |
| 16 | a\_vencer\_de\_1801\_ate\_5400\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer entre 1801 e 5400 dias | 0,00 |
| 17 | a\_vencer\_acima\_de\_5400\_dias | Faixa a vencer | DECIMAL(18,2) | Saldo a vencer acima de 5400 dias | 0,00 |
| 18 | carteira\_a\_vencer | Total | DECIMAL(18,2) | Total a vencer (soma das 6 faixas a vencer) | 1198363,68 |
| 19 | vencido\_de\_15\_ate\_90\_dias | Faixa vencida | DECIMAL(18,2) | Saldo vencido há 15 a 90 dias | 0,00 |
| 20 | vencido\_acima\_de\_90\_dias | Faixa vencida | DECIMAL(18,2) | Saldo vencido há mais de 90 dias | 3412,54 |
| 21 | carteira\_vencida | Total | DECIMAL(18,2) | Total vencido (soma das 2 faixas vencidas) | 3412,54 |
| 22 | carteira\_ativa | Total | DECIMAL(18,2) | Carteira total: carteira\_a\_vencer + carteira\_vencida | 3454,46 |
| 23 | carteira\_inadimplencia | Risco | DECIMAL(18,2) | Carteira inadimplente. Na amostra, igual a vencido\_acima\_de\_90\_dias | 3412,54 |
| 24 | ativo\_problematico | Risco | DECIMAL(18,2) | Saldo classificado como ativo problemático. Definição a confirmar | 3412,54 |
