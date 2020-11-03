# Etapa 2 - Descrição da Proposta

## Slides da Proposta
Os slides da proposta pode sem vistos em [slides](slides/proposta_apresentação.pdf).

## Motivação e Contexto
Visualizar possíveis correlações entre o número de casos de DSTs, aspectos socioeconômicos e políticas públicas, a fim de determinar fatores chave no controle de novos casos.

## Método
Utilizar dados sobre novas infecções no Reino Unido através do site do governo e classificar de acordo com o agente causador (eg. vírus, bactéria, fungo) relacionando o nome da infecção com o grafo da DBpedia e por fim plotar gráfico de linhas de números de casos em função do ano, também nesse gráfico anotaremos medidas públicas relevantes para correlacionar avanço de casos e medidas do governo.

![criar-tabela-e-linkar-com-dbpedia](images/Metodologia-3.png)

![grafico-final](images/Metodologia-1.png)

Também vamos realizar outra análise, mas agora no contexto mundial e especificamente sobre a HIV, onde pegaremos aspectos socioeconômicos, como a escolaridade e a renda per capita, medidas públicas e vamos relacionar com o número de casos, buscando assim correlações entre o número de casos de HIV com medidas públicas e condições socioeconômicas. 

## Bases de Dados
| título da base           | link                                                                          | breve descrição             |
| ------------------------ | ----------------------------------------------------------------------------- | --------------------------- |
| AtlasPlus CDC            | https://www.cdc.gov/nchhstp/atlas/index.htm                                   | Tabela com dados sobre DSTs nos EUA |
| WHO GHO                  | https://apps.who.int/gho/athena/api/GHO                                       | Dados relevantes para o estudo sobre DST no mundo em JSON  |
| UK Gov Statistics        | https://www.gov.uk/government/statistics/                                     | Tabelas e textos sobre casos de DSTs no Reino Unido e medidas públicas |
| UNAIDS Laws and Policies | https://lawsandpolicies.unaids.org                                            | Dados sobre medidas públicas adotadas em cada país, inclusive sobre DSTs |
| DBpedia                  | http://dbpedia.org/page/Category:Sexually_transmitted_diseases_and_infections | Grafo sobre o tema DSTs da *DBpedia* |