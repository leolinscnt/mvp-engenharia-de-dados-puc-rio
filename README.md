# MVP: Análise de Voos Domésticos no Brasil - 2024

## Sumário
- Objetivo
- Problema a ser resolvido
- Perguntas de Negócio
- Arquitetura da Solução
- Coleta dos Dados
- Modelagem de Dados
- Camada Bronze – dados brutos
- Camada Silver – dados tratados e enriquecidos
- Construção manual da base de companhias aéreas
- Camada Gold – visão analítica consolidada
- Catálogo de Dados
- Qualidade dos dados
- Resultados das Perguntas de Negócio
- Autoavaliação

## Objetivo
O objetivo deste MVP é construir um pipeline de dados completo em nuvem utilizando Databricks, desde ingestão, transformação, modelagem e análise, permitindo responder perguntas de negócio relacionadas ao comportamento do histórico de voos domésticos no Brasil ao longo do ano de 2024.

## Problema a ser resolvido
O setor aéreo é influenciado por diversos fatores, como sazonalidade, região, demanda e capacidade operacional. Este MVP busca avaliar o comportamento dos voos domésticos no Brasil em 2024, identificando padrões operacionais, sazonalidade e concentração do mercado.

Com a análise, será possível compreender:
- Onde o setor aéreo é mais intenso;
- Quais aeroportos concentram maior atividade;
- Quais rotas são mais movimentadas;
- Quais empresas dominam o mercado;
- Como evolui o fluxo de transporte ao longo do ano.

## Perguntas de Negócio

### Volume e malha aérea
1. Quantos voos domésticos foram realizados por mês em 2024?
2. Quais aeroportos concentraram o maior volume de partidas?
3. Quais aeroportos concentraram o maior volume de chegadas?
4. Qual a participação percentual dos 10 maiores aeroportos no total de voos?

### Sazonalidade
5. Como o volume mensal de voos variou ao longo do ano?
6. Quais estações do ano apresentam maior e menor movimento aéreo?

### Conectividade
7. Quais pares origem–destino tiveram maior volume de voos?
8. Quais cidades apresentam maior diversidade de destinos atendidos?

### Companhias aéreas
9. Quais companhias aéreas operaram o maior número de voos domésticos?
10. Existe concentração da operação em poucas companhias?

## Arquitetura da Solução

### Processamento
O pipeline foi construído seguindo o modelo **Bronze → Silver → Gold**:

- **Bronze:** ingestão dos arquivos CSV, padronização de colunas e armazenamento inicial em Delta.
- **Silver:** tratamento e enriquecimento dos dados (conversão de datas, remoção de voos internacionais, normalização e derivação de colunas temporais).
- **Gold:** consolidação final em uma tabela analítica única (`voos_flat`), integrando informações de voos, aeroportos e companhias.

### Tecnologias e Bibliotecas
- Databricks
- PySpark (SQL + DataFrames)
- `pyspark.sql.functions` / `pyspark.sql.types`
- Consultas SQL

### Armazenamento
- Delta Lake

## Coleta dos Dados
Foram utilizadas duas bases públicas disponibilizadas pela [ANAC](https://www.gov.br/anac/pt-br/assuntos/dados-e-estatisticas):

1. Histórico de voos realizados no Brasil (domésticos e internacionais), incluindo companhia aérea, aeroportos de origem/destino, horários e status.
2. Dados operacionais e geográficos dos aeroportos brasileiros.

A extração foi manual a partir de arquivos CSV, que foram enviados para o Databricks como arquivos fonte para o Data Lake.

## Modelagem de Dados

### Camada Bronze – Dados brutos
- Armazenamento dos arquivos originais da ANAC.
- Renomeação e padronização mínima de colunas.
- Criação de tabelas em formato Delta.

### Camada Silver – Dados tratados e enriquecidos
- Conversão de datas/horas de string para timestamp.
- Filtragem para voos domésticos ativos.
- Criação de flag de "voo realizado".
- Seleção de colunas analíticas relevantes.
- Novas colunas derivadas: ano, mês e estação do ano.
- Checagem de nulos e consistência de ICAO, aeroportos e timestamps.
- Construção manual da base de companhias aéreas para reduzir inconsistências.

### Camada Gold – Visão analítica consolidada
- Tabela flat consolidando: voos, aeroportos de origem/destino e companhias aéreas.
- Tabela final: `mvp.gold.voos_flat` (~800 mil linhas de voos domésticos 2024).
- Permite responder todas as perguntas de negócio via SQL no Databricks.
- Foco nas análises em voos domésticos realizados, mantendo histórico completo.

## Catálogo de Dados
- Dados sólidos e consistentes provenientes das fontes oficiais da ANAC.
- Transformações mínimas e pontuais: padronização de colunas e conversão de datas.
- Base de aeroportos robusta, com excelente cobertura geográfica.
- Joins resultaram em uma tabela flat confiável, pronta para análise.

## Resultados das Perguntas de Negócio

1. **Voos por mês:** Julho teve maior volume (~69 mil), fevereiro menor (~59 mil), evidenciando sazonalidade.
2. **Aeroportos com maior volume de partidas:** concentração no Sudeste, especialmente em São Paulo.
3. **Aeroportos com maior volume de chegadas:** padrão similar ao das partidas, dominado pelo Sudeste.
4. **Participação dos 10 maiores aeroportos:** ~64,23% do total de voos, indicando forte concentração operacional.
5. **Variação mensal de voos:** queda em fevereiro, pico em julho, estabilidade nos demais meses.
6. **Estações com maior/menor movimento:** inverno (maior), verão e primavera (médias), outono (menor).
7. **Pares origem–destino mais movimentados:** São Paulo/Congonhas ↔ Rio de Janeiro/Santos Dumont, seguido por São Paulo ↔ Brasília/Confins/Curitiba.
8. **Cidades com maior diversidade de destinos:** Campinas, Confins, Guarulhos e Congonhas.
9. **Companhias com maior número de voos:** Azul, LATAM e Gol.
10. **Concentração do mercado:** as três companhias somam ~94% dos voos domésticos, mostrando alta concentração.

## Autoavaliação
- Primeiro MVP na pós-graduação, importante para transição de carreira.
- Desenvolvimento do pipeline completo no Databricks, consolidando ingestão, transformação e camada Gold.
- Pontos de melhoria: aprofundamento em PySpark, visualizações avançadas, manipulação de dados em escala.
- Futuras evoluções:
  - Ampliação da análise histórica com anos anteriores;
  - Inclusão de voos internacionais para comparação de malha e sazonalidade.




