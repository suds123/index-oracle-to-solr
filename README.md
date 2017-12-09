# All things Data

This repository describes the search for an ideal fast-search-analytics product which can sit between a traditional RDBMS or hadoop filesystem and application, and provide under 100ms query performance, text search capability, analytics, high availability etc.

# Proposed architecture
This picture depicts what architecture/capability we are after:

![Integral Layer](images/integral.png)

# Product comparisons
This table shows high level requirement for the product.

|Product|In-Memory|Persistence|Sec. Indexing|Text Search|Scalable|Availability|DR (CDCR)| Lang.|Release|
|-------|---------|-----------|-------------|-----------|--------|------------|---------|------|-------|
|Solr   | ✖ cache | ✔        | ✔           | ✔✔✔ Lucene| Horizontal  |✔  |✔  | Java |  |    
|Elasticsearch| ✖ cache| ✔   | ✔           | ✔✔✔ Lucene| Horizontal |✔  | ✔ | Java |   |  
|Oracle Text | ✖ cache| ✔    | ✔           | ✔✔ Inverted| Vertical | ✖  |✔  | C  |  |  
|Postgresql| ✖    | ✔        | ✔           | ✔        | Horizontal| ✔ | ✔ | C | 1996 |  
|MongoDB  | ✔ store|          | ✔          | ✔         | Horizontal | ✔ |  ✔| C++ |2009  |
|Ignite (GG)| ✔ store | ✔    | ✔           | ✔✔ Lucene | Horizontal | ✔ |✔  |Java | 2007| 
|EXASOL| ✔ store |            | ✔           | ✖        | Horizontal | ✔ | ✔ |  |2000  |  
|VoltDB| ✔       |            |             | ✖        | Horizontal | ✔ | ✔ |Java  |  |  
|Vertica|✖       | ✔         | ✔           | ✖        | Horizontal | ✔ |✔  |  | 2005 |  
|MariaDB|✖       | ✔         | ✔           | ✖        | Horizontal |✔  |✔  | C | 2009 |  
|Cassandra|✖     | ✔         |             | ✖        | Horizontal |✔  | ✔ |  Java | 2008 |  
|Sphinx| ✖ cache | ✔         | ✔           | ✔✔✔     | Horizontal | ✔ | ✔ | C++ |2001 |  

# Product evaluation
For the purpose of evaluation we have selected Solr, Elasticsearch, Oracle (Text) and Apache Ignite.

## Solr
 [Install SolrCloud](install-solr-cloud/README.md)
 
 [Index Oracle DB on Solr](index-oracle-db/README.md)
 
 [Solr security](security/README.md)
 
 [Solr performance](solr-performance/README.md)
 
## Elasticsearch


## Oracle (Text)


## Apache Ignite

# Product recommendation
