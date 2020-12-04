# Análise de novas infecções por HIV em agrupamentos geográficos usando rede de grafos

## Preparando o grafo

Adicionando dados de cada país (1 nó = país)

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/data/Filtered%20Data/countries.csv' AS line
CREATE (:Country {name: line.country, region: line.region, label: line.label, infections: -1})
~~~

Adicionado dados de infecções por HIV no ano de 2015.

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/data/Filtered%20Data/infections.csv' AS infections

MATCH (a:Country {label: infections.region})
WHERE infections.year = '2015'
SET a.infections = infections.nominal
~~~

Criando arestas que conectam países vizinhos.

~~~cypher
LOAD CSV with headers FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/alteracoes-serodio/stage04/data/processed/neighbours.csv' as line
MATCH(a:Country {label: line.country})
MATCH(b:Country {label: line.neighbour})
MERGE (a)<-[:Neighbour]->(b)
~~~

## Utilizando Louvain para agrupar países com dados socioeconômicos parecidos

Criação do grafo para utilização do Louvain.

~~~cypher
CALL gds.graph.create(
  'neighbourhoodGraph',
  'Country',
  {
    Neighbour: {
      orientation: 'UNDIRECTED'
    }
  }
)
~~~

Utilização do Louvain para agrupar países em comunidades utilizando as arestas de fronteiras para isso. Retonar um arquivo CSV com os (`label`, `name`, `region`, `infections`, `communityId`) de cada país e será utilizado do *Cytoscape* para vizualização do grafo.

~~~cypher
CALL gds.louvain.stream('neighbourhoodGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).label AS label, gds.util.asNode(nodeId).name AS name, gds.util.asNode(nodeId).region AS region, gds.util.asNode(nodeId).infections AS infections, communityId
ORDER BY communityId ASC
~~~

## Exportação de dados para criação da rede

Retorna o CSV dos países vizinhos com as colunas `source` e `target`, que será utilizado para criar a rede no *Cytoscape*.

~~~cypher
MATCH (a:Country)-[:Neighbour]->(b:Country)
RETURN a.label as source, b.label as target
~~~
