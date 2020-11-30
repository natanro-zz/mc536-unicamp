## Exercise 1

Compute the Pagerank of the Wikipedia example in Cypher:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:ExWiki {name:line.source})
MERGE (p2:ExWiki {name:line.target})
CREATE (p1)-[:LinkExWiki]->(p2)
~~~

~~~cypher
CALL gds.graph.create(
  'ExWikiGraph',
  'ExWiki',
  'LinkExWiki'
)
~~~

~~~cypher
CALL gds.pageRank.stream('ExWikiGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC
~~~

~~~cypher
CALL gds.pageRank.stream('ExWikiGraph')
YIELD nodeId, score
MATCH (e:ExWiki {name: gds.util.asNode(nodeId).name})
SET e.pagerank = score
~~~

~~~cypher
CALL gds.pageRank.stream('ExWikiGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score AS pagerank
~~~

## Exercise 2 - FAERS & DRON

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results.

~~~cypher
CALL gds.graph.create(
  'Ex2Graph',
  'Drug',
  {
    Relates: {
      orientation: 'UNDIRECTED'
    }
  }
)
~~~

~~~cypher
CALL gds.louvain.stream('Ex2Graph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC
~~~

~~~cypher
CALL gds.louvain.stream('Ex2Graph')
YIELD nodeId, communityId
MATCH (d:Drug {name: gds.util.asNode(nodeId).name})
SET d.community = communityId
~~~