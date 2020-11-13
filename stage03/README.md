# Etapa 3 - Primeiros Modelos e Análises

## Slides da Proposta
Os slides da proposta pode sem vistos em [slides](slides/proposta_apresentação.pdf).

## Motivação e Contexto
Visualizar possíveis correlações entre o número de casos de DSTs, aspectos socioeconômicos e políticas públicas, a fim de determinar fatores chave no controle de novos casos.

## Método
[TODO]: <> (atualizar a metodologia do Reino Unido assim que possível!)
Utilizar dados sobre novas infecções no Reino Unido através do site do governo e classificar de acordo com o agente causador (eg. vírus, bactéria, fungo) relacionando o nome da infecção com o grafo da DBpedia e por fim plotar gráfico de linhas de números de casos em função do ano, também nesse gráfico anotaremos medidas públicas relevantes para correlacionar avanço de casos e medidas do governo.

Também realizaremos uma análise no contexto mundial, onde iremos relacionar diversos aspectos socioeconômicos (como distribuição do PIB através do valor per capita e o índice de desigualdade econômica, Gini, e também através do IDH) e medidas públicas (como testes de HIV e disponibilidade de tratamento retroviral) com o número de casos de HIV em cada país em um determinado ano.

### Modelos Conceituais

![conceitual-1](images/conceitual-1.png)
#### Modelo conceitual para os dados de infecções por HIV no mundo.

![conceitual-2](images/conceitual-2.png)
#### Modelo conceitual para infecções de DSTs no Reino Unido.

### Modelos Lógicos
![logico](images/logico.png)
#### Modelos lógicos utilizados para o projeto
Nas tabelas Testagem, Tratamento, Prevenção e Legislação, muitas colunas foram omitidas devido à sua quantidade.

### Tratamento de Dados

Os notebooks de extração e tratamento de dados pode ser verificados na [pasta](notebook/tratamento-dados/) e os dados de saída se encontram em [Filtered Data](../data/Filtered%20Data/). Os arquivos ODS obtidos na base de dados UK Gov Statistics – referentes aos casos de DSTs no Reino Unido – e o arquivo csv da UNAIDS Laws and Policies – referentes aos dados de medidas públicas – encontram se na pasta [data](../data/). Esses dados foram tratados e filtrados, respectivamente em [uk_data.ipynb](notebook/tratamento-dados/uk_data.ipynb) e [getPoliciesData.ipynb](notebook/tratamento-dados/getPoliciesData.ipynb). Os dados socioeconômicos de cada país, retirados da DBPedia em formato de grafos, foram extraídos em [getCountryData.ipynb](notebook/tratamento-dados/getCountryData.ipynb), e os dados de infecções em JSON foram extraídos pelo uso da API Athena em [newInfectionsWHO.ipynb](notebook/tratamento-dados/newInfectionsWHO.ipynb).

### Queries

As queries podem ser vistas na [pasta de queries](notebook/sql) e seus resultados foram convertidos em arquivos csv para simplificar a visualização e se encontram na pasta [saida](saida/). Especificamente, o notebook [PoliticasxRenda.ipynb](notebook/sql/PoliticasxRenda.ipynb) faz uma query que relaciona o número de políticas públicas por classificação de renda, cujo resultado pode ser visto no arquivo [politicas-x-renda.csv](saida/politicas-x-renda.csv). O notebook [RegiaoxInfeccoes.ipynb](notebook/sql/RegiaoxInfeccoes.ipynb) faz uma query que relaciona o número de infecções de HIV em cada região a cada ano, como pode ser visto em [regiao-x-infeccoes.csv](saida/regiao-x-infeccoes.csv), e o notebook [RendaxInfeccoes.ipynb](notebook/sql/RendaxInfeccoes.ipynb) relaciona o número de infecções com dados socioeconômicos dos países, como mostrado em [socioeconomico-x-infeccoes.csv](saida/socioeconomico-x-infeccoes.csv). Por fim, o notebook [policies-query.ipynb](notebook/sql/policies-query.ipynb) faz relações entre o número de camisinhas distribuídas por habitante e as políticas associadas a camisinhas de cada país, a porcentagem de países de cada região que têm ou não políticas de distribuição de camisinhas, a porcentagem de paises em cada região que tem políticas nacionais de combate à AIDS e a porcentagem de países por categoria de renda que permitem o início do tratamento antiretroviral no dia do diagnóstico de HIV, respectivamente, em [numero-de-camisinhas-politicas.csv][saida/numero-de-camisinhas-politicas.csv], [distribuicao-regiao.csv](saida/distribuicao-regiao.csv), [estrategia-regiao.csv](saida/estrategia-regiao.csv) e [tratamento-classificacao.csv](saida/tratamento-classificacao.csv).

## Bases de Dados
| Título da base           | Link                                                                          | Breve descrição             |
| ------------------------ | ----------------------------------------------------------------------------- | --------------------------- |
|<s>AtlasPlus CDC</s>            | ~~https://www.cdc.gov/nchhstp/atlas/index.htm~~                                   | ~~Tabela com dados sobre DSTs nos EUA~~ |
| WHO GHO                  | https://apps.who.int/gho/athena/api/GHO                                       | Infecções por HIV no mundo e também informações algumas informações dos países como a região e línguas oficiais  |
| UK Gov Statistics        | https://www.gov.uk/government/statistics/                                     | Tabelas e textos sobre casos de DSTs no Reino Unido e medidas públicas |
| UNAIDS Laws and Policies | https://lawsandpolicies.unaids.org                                            | Dados sobre medidas públicas adotadas em cada país, inclusive sobre DSTs |
| DBpedia                  | http://dbpedia.org/data/| Dados socioeconômicos de cada país, como o PIB per capita, Gini e o IDH |