# Etapa 05 - Entrega Final

## Slides da Apresentação da Proposta

Os slides da proposta pode sem vistos em [slides](./slides/etapa-final.pdf).

## Modelo Conceitual Atualizado

### Modelo conceitual para os dados de infecções por HIV no mundo

![conceitual-1](assets/conceitual-1.png)

### Modelo conceitual para infecções de DSTs no Reino Unido (descartado)

![conceitual-2](assets/conceitual-2.png)

## Modelos Lógicos Atualizados

### Modelo Lógico Relacional

![logico-relacional](assets/logico-relacional.png)

Nas tabelas Testagem, Tratamento, Prevenção e Legislação, muitas colunas foram omitidas devido à sua quantidade.

### Modelo Lógico de Grafos

![logico-grafo](assets/logico-grafo.png)

## Programa de extração e conversão de dados atualizado

Os notebooks de extração e tratamento de dados pode ser verificados na [pasta](notebook/tratamento-dados/) e os dados de saída se encontram em [processed](data/processed). Os arquivos ODS obtidos na base de dados UK Gov Statistics – referentes aos casos de DSTs no Reino Unido – e o arquivo csv da UNAIDS Laws and Policies – referentes aos dados de medidas públicas – encontram se na pasta [external](data/external). Esses dados foram tratados e filtrados, respectivamente em [uk_data.ipynb](notebook/tratamento-dados/uk_data.ipynb) e [getPoliciesData.ipynb](notebook/tratamento-dados/getPoliciesData.ipynb). Os dados socioeconômicos de cada país, retirados da DBPedia em formato de grafos, foram extraídos em [getCountryData.ipynb](notebook/tratamento-dados/getCountryData.ipynb), e os dados de infecções em JSON foram extraídos pelo uso da API Athena em [newInfectionsWHO.ipynb](notebook/tratamento-dados/newInfectionsWHO.ipynb).

Na estap 4, utilizou-se mais um notebook para tratar dados. Os dados tratados são referentes a países vizinhos e pode ser visualizado em [CountriesNeighbourhood.csv](data/external/CountriesNeighbourhood.csv), o notebook utilizado foi [countryNeighbours.ipynb](notebook/tratamento-dados/countryNeighbours.ipynb) e a tabela resultante é [neighbours.csv](data/processed/neighbours.csv).

## Conjunto de queries de dois modelos

* Estágio 3
  
  As queries podem ser vistas na [pasta de queries](notebook/sql) e seus resultados foram convertidos em arquivos csv para simplificar a visualização e se encontram na pasta [saida](saida/). Especificamente no *estágio 3*, o notebook [PoliticasxRenda.ipynb](notebook/sql/PoliticasxRenda.ipynb) faz uma query que relaciona o número de políticas públicas por classificação de renda, cujo resultado pode ser visto no arquivo [politicas-x-renda.csv](saida/politicas-x-renda.csv). O notebook [RegiaoxInfeccoes.ipynb](notebook/sql/RegiaoxInfeccoes.ipynb) faz uma query que relaciona o número de infecções de HIV em cada região a cada ano, como pode ser visto em [regiao-x-infeccoes.csv](saida/regiao-x-infeccoes.csv), e o notebook [RendaxInfeccoes.ipynb](notebook/sql/RendaxInfeccoes.ipynb) relaciona o número de infecções com dados socioeconômicos dos países, como mostrado em [socioeconomico-x-infeccoes.csv](saida/socioeconomico-x-infeccoes.csv). Por fim, o notebook [policies-query.ipynb](notebook/sql/policies-query.ipynb) faz relações entre o número de camisinhas distribuídas por habitante e as políticas associadas a camisinhas de cada país, a porcentagem de países de cada região que têm ou não políticas de distribuição de camisinhas, a porcentagem de países em cada região que tem políticas nacionais de combate à AIDS e a porcentagem de países por categoria de renda que permitem o início do tratamento antirretroviral no dia do diagnóstico de HIV, respectivamente, em [numero-de-camisinhas-politicas.csv][saida/numero-de-camisinhas-politicas.csv], [distribuicao-regiao.csv](saida/distribuicao-regiao.csv), [estrategia-regiao.csv](saida/estrategia-regiao.csv) e [tratamento-classificacao.csv](saida/tratamento-classificacao.csv).

* Estágio 4
  
  Já no estágio 4, realizamos queries para o modelo lógico de grafos utilizando o *Neo4j* e essas queries estão na pasta [src](src). Na query [clustering_socioeconomics.md](src/clustering_socioeconomics.md), busca-se encontrar maneiras de agrupar diferentes países com características socioeconômicas (Gini, IDH e PIB per Capita PPC) semelhantes entre si para depois linkar diferentes grupos e tentar correlacionar a quantidade de infecções através desses dados. Para isso, ligamos países (vértices) com valor socioeconômico semelhante com arestas, cujas arestas têm peso maior conforme mais próximo são os valores, e esse grafo foi processado através de uma biblioteca com Louvain considerando ou não os pesos das arestas. As saídas foram convertidas em csv e depois os resultados foram processados no Cytoscape para a visualização, onde quanto maior o tamanho do vértice maior será o IDH do país e cores diferenciam diferentes grupos, lembrando que as cores escolhidas não tem uma lógica por trás, por exemplo a escolha por uma cor mais escura não quer dizer que um grupo é pior ou melhor que outro. As saídas são:
  * Grafo com arestas ligando países com IDH parecidos e classificando países através do Gini e IDH considerando o peso entre arestas [rel_idh_class_gini_idh_peso.png](./assets/rel_idh_class_gini_idh_peso.png);
  * Grafo com arestas ligando países com Gini parecidos e classificando países através do Gini e IDH considerando o peso entre arestas [rel_gini_class_gini_idh_peso.png](./assets/rel_gini_class_gini_idh_peso.png);
  * Grafo com arestas ligando países com IDH parecidos e classificando países através do Gini, do IDH e do PIB per Capita PPC considerando o peso entre arestas [rel_idh_class_tudo_peso.png](./assets/rel_idh_class_tudo_peso.png);
  * Grafo com arestas ligando países com Gini parecidos e classificando países através do Gini, do IDH e do PIB per Capita PPC considerando o peso entre arestas [rel_gini_class_tudo_peso.png](./assets/rel_gini_class_tudo_peso.png);
  * Grafo com arestas ligando países com IDH parecidos e classificando países do PIB e da desigualdade econômica (Gini) desconsiderando o peso entre arestas [rel_idh_class_money_peso.png](./assets/rel_idh_class_money_peso.png);
  * Grafo com arestas ligando países com Gini parecidos e classificando países do PIB e da desigualdade econômica (Gini) desconsiderando o peso entre arestas [rel_gini_class_money_peso.png](./assets/rel_gini_class_money_peso.png).

  Outra query tenta agrupar países cujas políticas públicas sejam semelhantes adicionando arestas entre dois países cujo peso é proporcional ao número de leis compartilhadas, assim o objetivo é tentar encontrar e correlacionar padrões na quantidade de políticas semelhantes com as características socioeconômicas do país. Primeiramente, são ligados todos os países pela quantidade de políticas que compartilha. Os vértices são agrupados conforme sua classe socioeconômica, como mostra a [imagem](./assets/total.png), sendo os nos cantos superior esquerdo, superior direito, inferior esquerdo e inferior direito países de renda alta, de renda baixa-média, renda baixa e média-alta, respectivamente. Devido à quantidade de vértices, não foi possível capturar os nomes dos países nas imagens. No gráfico, os vértices com coloração mais arroxeada têm IDH mais elevado, e arestas mais avermelhadas representam maior número de políticas coincidentes.
  Nota-se que os países com renda mais alta compartilham relativamente poucas políticas com países de outros grupos, como evidenciado pela cor clara de suas arestas. Além disso, há poucos países do grupo que têm muitas políticas iguais, o que pode ser visto pelas poucas arestas vermelhas na [imagem](./assets/highincome.png). Por outro lado, nota-se uma grande coincidência de políticas entre países de rendas baixa e baixa-média, e um grande número de países de baixa renda com políticas semelhantes, como pode ser visto em [lowincome.png](./assets/lowincome.png).

  Por fim, a última query cria um grafo ligando um país A com todos os países X tal que A e X compartilham fronteira (são países vizinhos). Criando essa fronteira, é possível analisar como os casos de infecção por HIV se distribuem geograficamente, permitindo buscar por regiões que apresentam grande número de casos. Algumas partes interessantes do grafo (OBS: países que não possuem dados de infecções no ano de 2015 estão com o nome em vermelho):
  * [África](assets/regional_infections_AFR.png);
  * [Ásia](assets/regional_infections_ASIApng.png);
  * [América do Sul](assets/regional_infections_SA.png).
  
## Escolha do modelo de grafos

  As queries desenvolvidas nessa etapa objetivam, em geral, a visualização e análise da correlação entre políticas públicas, dados socioeconômicos e infecções. O modelo de grafos permite uma melhor diferenciação entre agrupamentos de países, e a possibilidade de, por exemplo, ajustar o tamanho de um nó de acordo com alguma característica permite mais facilmente sua comparação. Além disso, esse formato simplifica a visualização da relação entre dados, o que seria difícil na análise de uma tabela no modelo relacional, especialmente para grandes volumes de dados: o grafo de países e políticas públicas, por exemplo, precisaria ser representado por uma tabela de cerca de 28900 linhas, que tornaria a análise desejada inviável. A visualização das relações entre países por arestas de um grafo também simplifica muito certas análises, como as de dados socioeconômicos, e permite encontrar similaridades entre os dados antes não percebidas, como por exemplo cada uma das componentes conexas nos grafos de IDH.

## Bases de Dados

| Título da base           | Link                                            | Breve descrição                                                                                                 |
| ------------------------ | ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| <s>AtlasPlus CDC</s>     | ~~https://www.cdc.gov/nchhstp/atlas/index.htm~~ | ~~Tabela com dados sobre DSTs nos EUA~~                                                                         |
| WHO GHO                  | https://apps.who.int/gho/athena/api/GHO         | Infecções por HIV no mundo e também informações algumas informações dos países como a região e línguas oficiais |
| <s>UK Gov Statistics</s>        | ~~https://www.gov.uk/government/statistics/~~       | ~~Tabelas e textos sobre casos de DSTs no Reino Unido e medidas públicas~~                                          |
| UNAIDS Laws and Policies | https://lawsandpolicies.unaids.org              | Dados sobre medidas públicas adotadas em cada país, inclusive sobre DSTs                                        |
| DBpedia                  | http://dbpedia.org/data/                        | Dados socioeconômicos de cada país, como o PIB per capita, Gini e o IDH                                         |

## Arquivos de Dados

 | Nome do Arquivo                                                             | Link                                                                                               | Breve descrição                                                                                                                                                                                                     |
 | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
 | ~~`2019_Table_1_New_STI_diagnoses_and_rates_in_England_by_gender.ods`~~         | ~~[arquivo](data/external/2019_Table_1_New_STI_diagnoses_and_rates_in_England_by_gender.ods)~~         | ~~Novos casos de DSTs na Inglaterra por gênero (2010-2019 Uk Gov Statistics)~~                                                                                                                                          |
 | ~~`2019_Table_4_All_STI_diagnoses_and_services_by_gender_and_sexual_risk.ods`~~ | ~~[arquivo](data/external/2019_Table_4_All_STI_diagnoses_and_services_by_gender_and_sexual_risk.ods)~~ | ~~Novos casos de DSTs na Inglaterra por gênero e risco sexual, com divisões em regiões (2015-2019 Uk Gov Statistics)~~                                                                                                  |
 | `NCPI downloads.csv`                                                        | [arquivo](data/external/NCPI%20downloads.csv)                                                      | Tabela de Leis e Políticas adotadas por países com relação ao HIV (UNAIDS)                                                                                                                                          |
 | `CountriesNeighbourhood.csv`                                                | [arquivo](data/external/CountriesNeighbourhood.csv)                                                | Grafo em formato CSV que liga países que são vizinhos entre si.                                                                                                                                                     |
 | `countries.csv`                                                             | [arquivo](data/processed/countries.csv)                                                            | Tabela com os dados dos países já processados (DBPedia + WHO GHO)                                                                                                                                                   |
 | `country_policies.csv`                                                      | [arquivo](data/processed/country_policies.csv)                                                     | Tabela com as políticas em cada país e seus respectivos valores (UNAIDS)                                                                                                                                            |
 | `neighbours.csv`                                                            | [arquivo](data/processed/neighbours.csv)                                                           | Grafo em formato CSV que liga países que são vizinhos entre si filtrado e tratado de maneira que os países são referenciados utilizando as labels como chave estrangeira para a tabela de países (`countries.csv`). |
 | `infections.csv`                                                            | [arquivo](data/processed/infections.csv)                                                           | Tabela com número de novas infecções por HIV em cada país (WHO GHO)                                                                                                                                                 |
 | `legislacao.csv`                                                            | [arquivo](data/processed/legislacao.csv)                                                           | Tabela com legislações relacionadas ao HIV por país (UNAIDS)                                                                                                                                                        |
 | `policies.csv`                                                              | [arquivo](data/processed/policies.csv)                                                             | Tabela com as políticas e seus respectivos tipos (UNAIDS)                                                                                                                                                           |
 | `prevencao.csv`                                                             | [arquivo](data/processed/prevencao.csv)                                                            | Tabela com políticas de prevenção do HIV por país (UNAIDS)                                                                                                                                                          |
 | `testagem.csv`                                                              | [arquivo](data/processed/testagem.csv)                                                             | Tabela com políticas de testagem do HIV por país (UNAIDS)                                                                                                                                                           |
 | `tratamento.csv`                                                            | [arquivo](data/processed/tratamento.csv)                                                           | Tabela com políticas de tratamento do HIV por país (UNAIDS)                                                                                                                                                         |
 | ~~`uk_sti_cases_per_100000.csv`~~                                               | ~~[arquivo](data/processed/uk_sti_cases_per_100000.csv)~~                                              | ~~Casos de DSTs no Reino Unido por 100000 habitantes (Uk Gov Statistics)~~                                                                                                                                              |
 | ~~`uk_sti_cases.csv`~~                                                          | ~~[arquivo](data/processed/uk_sti_cases.csv)~~                                                         | ~~Casos de DSTs no Reino Unido (Uk Gov Statistics)~~                                                                                                                                                                    |
 | `Number of new HIV infections`                                              | [link](https://apps.who.int/gho/athena/api/GHO/HIV_0000000026?format=json)                         | Novos casos de infecção por HIV (WHO GHO)                                                                                                                                                                           |
