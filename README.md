# Introduction

This repository describes the search for an ideal fast-search-analytics product which can sit between a traditional RDBMS or hadoop filesystem and application, and provide under 100ms query performance, text search capability, analytics, high availability etc.

# Proposed architecture
This picture depicts what architecture/capability we are after:

![Integral Layer](images/integral.png)

# Product comparisons
This table shows high level requirement for the product.

|Product|In-Memory|Disk |Sec Index |Columnar|Text Search|Scales|HA|CDCR| Lang.|Release|
|-------|---------|-----|----------|--------|-----------|------|--|----|------|-------|
|Solr   | ✖ cache | ✔  | ✔       |        | ✔✔✔ Lucene| Hor |✔  |✔  | Java | 2004 |    
|Elastic| ✖ cache| ✔   | ✔       |        | ✔✔✔ Lucene| Hor |✔  | ✔ | Java | 2004 |  
|Oracle(Text)| ✖ cache| ✔ | ✔    |        | ✔✔ Inverted| Ver| ✖ |✔  | C    |      |  
|PostgreSQL| ✖    | ✔ | ✔        |        | ✔         | Hor  | ✔ | ✔ | C   | 1996 |  
|MongoDB  | ✔ store|   | ✔        |        | ✔        | Hor  | ✔ |  ✔| C++  |2009  |
|Ignite(GG)| ✔ store | ✔ | ✔     |        | ✔✔ Lucene| Hor  | ✔ |✔  |Java  | 2007 | 
|EXASOL| ✔ store |       | ✔      |        | ✖        | Hor  | ✔ | ✔ |      |2000  |  
|VoltDB| ✔       |       |        |        | ✖        | Hor  | ✔ | ✔ |Java  |      |  
|Vertica|✖       | ✔    | ✔      |        | ✖        | Hor  | ✔ |✔  |      | 2005 |  
|MariaDB|✖       | ✔    | ✔      |        | ✖        | Hor  |✔  |✔  | C    | 2009 |  
|Cassandra|✖     | ✔    | ✖      |        | ✖        | Hor  |✔  | ✔ | Java | 2008 |  
|Sphinx| ✖ cache | ✔    | ✔      |        | ✔✔✔     | Hor  | ✔ | ✔ | C++  |2001  |  
|MemSQL|✔        | ✔    |        |  ✔     |           | Hor  | ✔ | ✔ | C++  | 2013 |

# Product evaluation
For the purpose of evaluation we have selected Solr, Elasticsearch, Oracle (Text) and Apache Ignite.

## Solr
 [Install SolrCloud](solr/install-solr-cloud/README.md)
 
 [Index Oracle DB on Solr](index-oracle-db/README.md)
 
 [Solr security](solr/security/README.md)
 
 [Solr performance](solr/performance/README.md)
 
## Elasticsearch


## Oracle (Text)


## Apache Ignite


# Product recommendation
