# Introduction

This repository describes the search for a high-performance-search-analytics product which can sit between a traditional RDBMS or hadoop filesystem and application, and provide under 100ms query performance, search capability, handle large volumes, analytics, high availability etc.

# Proposed architecture
This picture depicts where the product would sit within our existing architecture and interactions with other components:

![Integral Layer](images/integral.png)

# Product feature comparisons
This table shows high level requirement for the product. 
With what we are after it's unlikely a single product can satisfy all requirements, so it's likely there will be trade-offs therefore we have widen the scope to include a variety of data technologies.


|Product|In-Memory|Disk |Sec Index |Columnar|Text Search|Scales|HA|XDCR| Lang.|Release|
|-------|---------|-----|----------|--------|-----------|------|--|----|------|-------|
|Solr   | ✖ cache | ✔  | ✔       |        | ✔✔✔ Lucene| Hor |✔  |✔  | Java | 2004 |    
|Elastic| ✖ cache| ✔   | ✔       |        | ✔✔✔ Lucene| Hor |✔  | ✔ | Java | 2004 |  
|Oracle(Text)| ✖ cache| ✔ | ✔    |        | ✔✔ Inverted| Ver| ✖ |✔  | C    | 1997 |  
|PostgreSQL| ✖    | ✔ | ✔        |        | ✔         | Hor  | ✔ | ✔ | C   | 1996 |  
|MongoDB  | ✔ store|   | ✔        |        | ✔        | Hor  | ✔ |  ✔| C++  |2009  |
|Ignite(GG)| ✔ store | ✔ | ✔     |        | ✔✔ Lucene| Hor  | ✔ |✔  |Java  | 2007 | 
|EXASOL| ✔ store |       | ✔      |  ✔    | ✖        | Hor  | ✔ | ✔ |      |2000  |  
|VoltDB| ✔       |       |        |        | ✖        | Hor  | ✔ | ✔ |Java  |      |  
|Vertica|✖       | ✔    | ✔      |  ✔     | ✖        | Hor  | ✔ |✔  |      | 2005 |  
|MariaDB|✖       | ✔    | ✔      |        | ✖        | Hor  |✔  |✔  | C    | 2009 |  
|Cassandra|✖     | ✔    | ✖      |        | ✖        | Hor  |✔  | ✔ | Java | 2008 |  
|Sphinx| ✖ cache | ✔    | ✔      |        | ✔✔✔     | Hor  | ✔ | ✔ | C++  |2001  |  
|MemSQL|✔        | ✔    |        |  ✔     |           | Hor  | ✔ | ✔ | C++  | 2013 |
|Couchbase|✔     | ✔    | ✔      |        | ✔✔       | Hor  | ✔ | ✔ | C++  | 2010 |

# Product evaluation
For the purpose of evaluation we have selected Solr, Elasticsearch, Oracle (Text) and Apache Ignite.

## Solr
  1. [Install SolrCloud](solr/install-solr-cloud/README.md)
 
  2. [Index RDBMS on Solr](index-oracle-db/README.md)
 
  3. [Solr security](solr/security/README.md)
 
  4. [Solr performance](solr/performance/README.md)
  
  5. [Solr commands reference](solr/commands/README.md)
 
## Elasticsearch
  1. [Install Elastic cluster]  <!--(elastic/install-cluster/README.md)-->
 
  2. [Index RDBMS on Elastic]  <!--(elastic/index-oracle-db/README.md)-->
 
  3. [Elastic security]  <!--(elastic/security/README.md)-->
 
  4. [Elastic performance]  <!--(elastic/performance/README.md)-->
  
  5. [Elastic commands reference]  <!--(elastic/commands/README.md)-->

## Oracle (Text)
  1. [Configure Oracle Text]  <!--(oracle-text/configure-text/README.md)-->
 
  2. [Create Text Index]  <!--(oracle-text/index-oracle-db/README.md)-->
 
  3. [Text security]  <!--(oracle-text/security/README.md)-->
 
  4. [Text performance]  <!--(oracle-text/performance/README.md)-->
  
  5. [Text commands reference]  <!--(oracle-text/commands/README.md)-->

## Apache Ignite
  1. [Install Ignite]  <!--(ignite/install-ignite/README.md)-->
 
  2. [Load RDBMS to Ignite]  <!--(ignite/index-oracle-db/README.md)-->
 
  3. [Ignite security]  <!--(ignite/security/README.md)-->
 
  4. [Ignite performance]  <!--(ignite/performance/README.md)-->
  
  5. [Ignite commands reference]  <!--(ignite/commands/README.md)-->

# Product recommendation

The products were tested on a single VM on GCP (4 vCPUs + 15GB RAM) with 27 million records. 

|Selection criteria|Solr|Elasticsearch|Oracle(Text)|Ignite|
|------------------|----|-------------|------------|------|
|Infrastructure implications|new VMs + SSD|new VMs + SSD |existing DB |new VMs + SSD |
|security (functional) | | | | |
|security (data)| Query filter | Query filter| Query filter | Query filter |
|functional fit across our repositories to index| | | | |
|performance of queries | | | | |
|performance of complex queries| | | | |
|performance of load|~5000/s, 30 stored + 19 indexed fields| | | |
|maintainability | | | | |
|supportability| | | | |
|product support| ✔ (Lucid) | ✔ | ✔ | ✔ (GG)|
|knowledge community| | | | |
|maturity | ✔ | ✔ | ✔ | ✔ (GG)|
|TCO| | |  | |
|deployability in our environment| ✔ | ✔ | ✔✔ | ✔ |
|source support-RDBMS| ✔ | ✔ | ✔ | ✔ |
|source support-OID,LDAP| | | | |
|source support-logs| ✔ | ✔ |SQL\*Loader | |
|source support-CMS| | | | |
|source support-files| ✔ | ✔ | ✔ | ✔ |
|source support-JMS| | | | |
