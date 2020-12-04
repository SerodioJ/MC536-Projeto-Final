# Relacionando as classes socioeconômicas por número médio de políticas compartilhadas entre os países

## Preparando o grafo

### Adicionando dados socioeconômicos de cada país (1 nó = país)

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/stage04/data/processed/countries.csv' AS line
CREATE (:Country {country: line.country, label: line.label, wb_class: line.wb_class, gini: line.gini, gdbPPPperCapita: line.gdbPPPperCapita, hdi: line.hdi, populationTotal: line.populationTotal})
~~~

### Adicionando dados das políticas de cada país (1 nó = 1 relação país-política)

~~~cypher
LOAD CSV with headers FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/stage04/data/processed/country_policies.csv' AS line
CREATE (pol: Pol {country: line.country, politica: line.policyId, isNumeric: line.isNumeric, value: line.value})
~~~

### Criando arestas que ligam dois países pelo número de políticas compartilhadas

~~~cypher
match (CP1:Pol)
match (CP:Pol)
MATCH (c1:Country)
MATCH (c:Country)
WHERE c.country <> c1.country AND c.country = CP.country AND c1.country = CP1.country AND CP1.country <> CP.country AND CP.politica IS NOT NULL AND CP1.politica IS NOT NULL AND CP.politica = CP1.politica AND CP.value = CP1.value
MERGE (c1)-[t:Politicas]->(c)
ON CREATE SET t.weight=1
ON MATCH SET t.weight=t.weight+1
~~~

O grafo gerado pela query acima foi utilizado também para comparar os países como grupos

### Adicionando dados das classificações socioeconômicas

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/stage04/data/processed/countries.csv' AS line
CREATE (:Classe {wb_class: line.wb_class})

load csv with headers from 'https://raw.githubusercontent.com/SerodioJ/MC536-Projeto-Final/master/stage04/data/processed/countries.csv' as csv
merge (c:Classe {wb_class: csv.wb_class})
on match set c.wb_class =  csv.wb_class
~~~

### Relacionando os nós "Classe" pela média de políticas públicas

~~~cypher
match (cl1: Classe)
match (cl2: Classe)
match (c1)-[t:Politicas]->(c2)
where cl1.wb_class <> cl2.wb_class AND c1.wb_class = cl1.wb_class AND c2.wb_class = cl2.wb_class
MERGE (cl1)-[p:PoliticasClasses]->(cl2)
ON CREATE SET p.total = t.weight, p.average = t.weight, p.n = 1
ON MATCH SET p.total = p.total + t.weight, p.n = p.n + 1, p.average = p.total/p.n
~~~
