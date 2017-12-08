# All things Data

This repository describes the search for an ideal fast-search=analytics product which can sit between a traditional RDBMS or hadoop filesystem and application, and provide under 100ms query performance, text search capability, analytics, high availability etc.

|Product|In-Memory|Persistence|Sec. Indexing|Text Search|Scalable|Availability|DR (CDCR)| InUse@WP|License|Lang.|Release|
|-------|---------|-----------|-------------|-----------|--------|------------|---------|---------|-------|-----|-------|
|Solr   | ✖ cache | ✔        | ✔           | ✔✔✔ Lucene| Horizontal  |  |  |  |  |   |   | 
|Elasticsearch| ✖ cache| ✔   | ✔           | ✔✔✔ Lucene| Horizontal |  |  |      |   |  |  | 
|Oracle Text | ✖ cache| ✔    | ✔           | ✔✔ Inverted| Vertical |  |  |       |  |  |   | 
|Postgresql| ✖    | ✔        | ✔           | ✔        | Horizontal|  |  |  |  |  |       | 
|MongoDB  | ✔ store|          | ✔          | ✔         | Horizontal |  |  |  |  |  |   | 
|Ignite (GG)| ✔ store | ✔    | ✔           | ✔✔ Lucene | Horizontal |  |  |  |  |  |   | 
|EXASOL| ✔ store |            | ✔           | ✖        | Horizontal |  |  |  |  |  |   | 
|VoltDB| ✔       |            |             | ✖        | Horizontal |  |  |  |  |  |   | 
|Vertica|✖       | ✔         | ✔           | ✖        | Horizontal |  |  |  |  |  |   | 
|MariaDB|✖       | ✔         | ✔           | ✖        | Horizontal |  |  |  |  |  |   | 
|Redisearch|      | ✔         |✔           | ✔✔       | Horizontal  |  |  |  |  |  |   | 
|Cassandra|✖     | ✔         |             | ✖        | Horizontal |  |  |  |  |  |   | 
|Sphinx| ✖ cache | ✔         | ✔           | ✔✔✔     | Horizontal |  |  |  |  |  |   | 


 [Install SolrCloud](install-solr-cloud/README.md)
 
 [Index Oracle DB on Solr](index-oracle-db/README.md)
 
 [Solr security](security/README.md)
 
 [Solr performance](solr-performance/README.md)
