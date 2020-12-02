# Classificação dos países por meio de dados socioeconomicos em rede de grafos

## Preparando o grafo

Adicionando dados socioeconômicos de cada país (1 nó = país)
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/data/Filtered%20Data/countries.csv' AS line
CREATE (:Country {name: line.country, region: line.region, gini: line.gini, gdb: line.gdbPPPperCapita, hdi: line.hdi, pop: line.populationTotal})
~~~

Criando arestas que ligam dois países com valor de gini próximos entre si (diferença menor que 10) e quanto mais próximo, maior é o peso.
~~~cypher
MATCH (a: Country)
MATCH (b: Country)
WHERE a.name < b.name AND a.gini IS NOT NULL AND b.gini IS NOT NULL AND abs(toInteger(a.gini) - toInteger(b.gini)) < 10
CREATE (a)-[i:Inequality]->(b)
SET i.weight = 10 - abs(toInteger(a.gini) - toInteger(b.gini))
~~~

Criando arestas que ligam dois países com valor de IDH próximos entre si (diferença menor que 10) e quanto mais próximo, maior é o peso.
~~~cypher
MATCH (a: Country)
MATCH (b: Country)
WHERE a.name < b.name AND a.hdi IS NOT NULL AND b.hdi IS NOT NULL AND abs(toInteger(a.hdi) - toInteger(b.hdi)) < 10
CREATE (a)-[h: HumanDev]->(b)
SET h.weight = 10 - abs(toInteger(a.hdi) - toInteger(b.hdi))
~~~

Criando arestas que ligam dois países com valor de PIB per Capita próximos entre si (diferença menor que 10) e quanto mais próximo, maior é o peso.
~~~cypher
MATCH (a: Country)
MATCH (b: Country)
WHERE a.name < b.name AND a.gdb IS NOT NULL AND b.gdb IS NOT NULL AND abs(toInteger(a.gdb) - toInteger(b.gdb)) < 1000
CREATE (a)-[m :MoneyPerCapita]->(b)
SET m.weight = 1000 - abs(toInteger(a.gdb) - toInteger(b.gdb))
~~~


## Utilizando Louvain para agrupar países com dados socioeconômicos parecidos

Utilizamos uma biblioteca no *Neo4j* com o algoritmo de Louvain para tentar agrupar diferentes países de acordo com dados socioeconômicos semelhantes enviando os nós e as arestas desejadas com ou sem peso, retornando o resultado dentro do próprio nó para facilitar a importação no *Cytoscape*.

### Classificação utilizando os valores de Gini e IDH de um país

Utilizamos esses dois valores, pois temos exemplos de países com IDH muito alto, porém tem uma desigualdade econômica alta como os EUA e a Austrália. 

~~~cypher
CALL gds.graph.create(
  'c_gini_idh',
  'Country',
  {
    HumanDev: {
      orientation: 'UNDIRECTED'
    },
    Inequality: {
      orientation: 'UNDIRECTED'
    }
  }
)

CALL gds.louvain.stream('c_gini_idh')
YIELD nodeId, communityId
MATCH (c:Country {name: gds.util.asNode(nodeId).name})
SET c.gini_idh = communityId
~~~

~~~cypher
CALL gds.graph.create(
  'c_gini_idh_weighted',
  'Country',
  {
    HumanDev: {
      orientation: 'UNDIRECTED'
    },
    Inequality: {
      orientation: 'UNDIRECTED'
    }
  },
  {
    relationshipProperties: 'weight'
  }
)

CALL gds.louvain.stream('c_gini_idh_weighted', { relationshipWeightProperty: 'weight' })
YIELD nodeId, communityId
MATCH (c:Country {name: gds.util.asNode(nodeId).name})
SET c.giw = communityId
~~~

### Utilizando IDH, Gini e PIB per Capita PPP para agrupar países

Nesse caso, tentamos analisar os diferentes grupos de acordo com os dados coletados sobre o *IDH*, *Gini* e *PIB per Capita PPP*, tentando agrupar países de acordo com o desenvolvimento humano, *IDH*, e a desigualdade econômica, *Gini*, e o *PIB per Capita PPP*, que é a média do PIB na população levando em conta o significado dele no poder de compra.

~~~cypher
CALL gds.graph.create(
  'everything_weighted',
  'Country',
  {
    HumanDev: {
      orientation: 'UNDIRECTED'
    },
    Inequality: {
      orientation: 'UNDIRECTED'
    },
    MoneyPerCapita: {
      orientation: 'UNDIRECTED'
    }
  },
  {
    relationshipProperties: 'weight'
  }
)

CALL gds.louvain.stream('everything_weighted', { relationshipWeightProperty: 'weight' })
YIELD nodeId, communityId
MATCH (c:Country {name: gds.util.asNode(nodeId).name})
SET c.ew = communityId
~~~

### Classificação de países puramente monetária

Nesse último caso, estamos procurando classificar países de acordo com o aspectos puramente monetários com os valores de *Gini* e *IDH per Capita PPP*. 

~~~cypher
CALL gds.graph.create(
  'money_weighted',
  'Country',
  {
    Inequality: {
      orientation: 'UNDIRECTED'
    },
    MoneyPerCapita: {
      orientation: 'UNDIRECTED'
    }
  },
  {
    relationshipProperties: 'weight'
  }
)

CALL gds.louvain.stream('money_weighted', { relationshipWeightProperty: 'weight' })
YIELD nodeId, communityId
MATCH (c:Country {name: gds.util.asNode(nodeId).name})
SET c.moneyw = communityId
~~~