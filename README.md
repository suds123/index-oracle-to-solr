# Index-Oracle-to-Solr

This is a demo for how to use “DataImport” tool provided by Apache Solr to index data from Oracle DB.

# Infrastructure
For this demo, I’ve used two virtual machines on Google Cloud Platform:
1. Oracle XE – 1 machine of type n1-standard-1 (1 vCPU, 3.75 GB memory) and 30GB standard disk. OS is Ubuntu 16.04 and Oracle 11.2.0.2.0 XE instance. 
2. Solr cloud – 1 machine of type - n1-standard-4 (4 vCPUs, 15 GB memory) and 20GB SSD disk. The VM is a certified prebuilt Solr image from Bitnami. OS is Debian 8 and packages installed are  Apache 2.4.29, Java 1.8.0_151, OpenSSL 1.0.2m, and Apache Solr 7.1.0

# Oracle
I have created a table in Oracle database named fact_transactions with 27 million records.

|COLUMN|DATA-TYPE|
|------|---------|
|OUTLET_ID|	VARCHAR2(10 BYTE)|
|OUTLET_NAME|	VARCHAR2(128 BYTE)|
|COMPANY|	VARCHAR2(10 BYTE)|
|COMPANY_NAME|	VARCHAR2(128 BYTE)|
|GROUP|	VARCHAR2(10 BYTE)|
|GROUP_NAME|	VARCHAR2(26 BYTE)|
|TRADING_DATE|	DATE|
|PROCESSING_DATE|	DATE|
|TRANSACTION_DATE|	DATE|
|CARD_NUMBER|	VARCHAR2(50 BYTE)|
|CARD_NUMBER_LEFT|	VARCHAR2(20 BYTE)|
|CARD_NUMBER_RIGHT|	VARCHAR2(20 BYTE)|
|CARD_EXPIRY_DATE|	VARCHAR2(26 BYTE)|
|ORIGINATORS_TRANSACTION_REF|	VARCHAR2(50 BYTE)|
|RECEIPT_NUMBER|	VARCHAR2(26 BYTE)|
|TICKET_NUMBER|	VARCHAR2(50 BYTE)|
|AUTH_CODE|	VARCHAR2(50 BYTE)|
|CARD_BRAND|	VARCHAR2(128 BYTE)|
|CARD_PRODUCT_TYPE_NAME|	VARCHAR2(26 BYTE)|
|CARD_PRODUCT_TYPE_CODE|	VARCHAR2(26 BYTE)|
|SCHEME|	VARCHAR2(26 BYTE)|
|TRANSACTION_TYPE_NAME|	VARCHAR2(50 BYTE)|
|TRANSACTION_SOURCE_NAME|	VARCHAR2(128 BYTE)|
|DEBIT_CREDIT|	VARCHAR2(10 BYTE)|
|CARD_SEQUENCE_NO|	VARCHAR2(3 BYTE)|
|ACQUIRED_PROCESSED|	VARCHAR2(26 BYTE)|
|SETTLEMENT_AMOUNT|	NUMBER(22,8)|
|SETTLEMENT_CURRENCY|	VARCHAR2(5 BYTE)|
|TRANSACTION_AMOUNT|	NUMBER(22,8)|
|TRANSACTION_CURRENCY|	VARCHAR2(5 BYTE)|

# SolrCloud
Bitnami certified Solr is installed at this path out-of-the-box - /opt/bitnami/apache-solr

Create a collection named corp-transactions with 2 replicas and 3 shards and using \_default configSet.
```
cd /opt/bitnami/apache-solr
bin/solr create -c corp-transactions -s 3 -rf 2
```

## Config
Create a configset directory and download the config.
```
mkdir configset
bin/solr zk downconfig -z localhost:9983 -n _default -d /opt/bitnami/apache-solr/configset
Downloading configset _default from ZooKeeper at localhost:9983 to directory /opt/bitnami/apache-solr/configset/conf

cd configset/conf
```

## Frequently used commands reference

Create Collection (N = number of shards, M = number of replicas)
```
bin/solr create -c <collection-name> -s N -rf M
```

Delete collection
```
bin/solr delete -c <collection-name>
```

Stop all nodes
```
bin/solr stop -all
```

Start / Restart SolrCloud
```
bin/solr restart -c -p 8983 -s example/cloud/node1/solr
bin/solr restart -c -p 7574 -z localhost:9983 -s example/cloud/node2/solr
bin/solr restart -c -p 8984 -z localhost:9983 -s example/cloud/node3/solr
```

Delete all documents
```
curl http://localhost:8983/solr/<collection-name>/update -H "Content-Type: text/xml" --data-binary '<delete><query>*:*</query></delete>'
```

Index a csv using post
```
bin/post -c <collection-name> <path and name of csv file>
```

