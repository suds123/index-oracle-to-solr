# Index-Oracle-to-Solr

This is a demo for how to use “DataImport” tool provided by Apache Solr to index data from Oracle DB.

# 1. Infrastructure
For this demo, I’ve used two virtual machines on Google Cloud Platform:
1. Oracle XE – 1 machine of type n1-standard-1 (1 vCPU, 3.75 GB memory) and 30GB standard disk. OS is Ubuntu 16.04 and Oracle 11.2.0.2.0 XE instance. 
2. Solr cloud – 1 machine of type - n1-standard-4 (4 vCPUs, 15 GB memory) and 20GB SSD disk. The VM is a certified prebuilt Solr image from Bitnami. OS is Debian 8 and packages installed are  Apache 2.4.29, Java 1.8.0_151, OpenSSL 1.0.2m, and Apache Solr 7.1.0

# 2. Oracle
We will create a table in Oracle database named fact_transactions with sample data.

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

# 3. SolrCloud
Bitnami certified Solr is installed at this path out-of-the-box - /opt/bitnami/apache-solr

Create a collection named corp-transactions with 2 replicas and 3 shards and using \_default configSet.
```
cd /opt/bitnami/apache-solr
bin/solr create -c corp-transactions -s 3 -rf 2
```

## 3.1 Config

#### 3.1.1 Create a configset directory and download the config.
```
mkdir configset
bin/solr zk downconfig -z localhost:9983 -n _default -d /opt/bitnami/apache-solr/configset
Downloading configset _default from ZooKeeper at localhost:9983 to directory /opt/bitnami/apache-solr/configset/conf

cd configset/conf
```

#### 3.1.2 Edit solrconfig.xml
Add DataImportHandler (first line) and ojdbc6.jar (last line)
```
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-dataimporthandler-.*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/contrib/extraction/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-cell-\d.*\.jar" />

  <lib dir="${solr.install.dir:../../../..}/contrib/clustering/lib/" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-clustering-\d.*\.jar" />

  <lib dir="${solr.install.dir:../../../..}/contrib/langid/lib/" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-langid-\d.*\.jar" />

  <lib dir="${solr.install.dir:../../../..}/contrib/velocity/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-velocity-\d.*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="ojdbc6.jar" />
```

Add Oracle requestHandler
```
  <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
    <lst name="defaults">
      <str name="config">oracle-data-config.xml</str>
    </lst>
  </requestHandler>	
```  

#### 3.1.3 Create oracle-data-config.xml
```
<dataConfig>
  <dataSource driver="oracle.jdbc.OracleDriver" url="jdbc:oracle:thin:@localhost:1521/xe" user="solardemo" password="xxx" />
  <document>
    <entity name="employees" query='select rownum as id,mid as outlet_id,outlet_name, company,company_name, group_name, trading_date, processing_date, card_number,card_number_left, card_number_right, card_expiry_date,transaction_date, originators_transaction_ref, ticket_number, transaction_type_name, transaction_source_name, receipt_number,settlement_amount, settlement_currency, transaction_amount, transaction_currency, auth_code, card_brand, card_product_type_name, card_product_type_code, scheme, debit_credit, card_sequence_no, acquired_processed, "GROUP" from fact_transactions'>
      <field column="id" name="id" />
      <field column="outlet_id" name="outlet_id" />
      <field column="outlet_name" name="outlet_name" />
      <field column="company" name="company" />
      <field column="company_name" name="company_name" />
      <field column="group_name" name="group_name" />
      <field column="trading_date" name="trading_date" />
      <field column="processing_date" name="processing_date" />
      <field column="card_number" name="card_number" />
      <field column="card_number_left" name="card_number_left" />
      <field column="card_number_right" name="card_number_right" />
      <field column="card_expiry_date" name="card_expiry_date" />
      <field column="transaction_date" name="transaction_date" />
      <field column="originators_transaction_ref" name="originators_transaction_ref" />
      <field column="ticket_number" name="ticket_number" />
      <field column="transaction_type_name" name="transaction_type_name" />
      <field column="transaction_source_name" name="transaction_source_name" />
      <field column="receipt_number" name="receipt_number" />
      <field column="settlement_amount" name="settlement_amount" />
      <field column="settlement_currency" name="settlement_currency" />
      <field column="transaction_amount" name="transaction_amount" />
      <field column="transaction_currency" name="transaction_currency" />
      <field column="auth_code" name="auth_code" />
      <field column="card_brand" name="card_brand" />
      <field column="card_product_type_name" name="card_product_type_name" />
      <field column="card_product_type_code" name="card_product_type_code" />
      <field column="scheme" name="scheme" />
      <field column="debit_credit" name="debit_credit" />
      <field column="card_sequence_no" name="card_sequence_no" />
      <field column="acquired_processed" name="acquired_processed" />
      <field column="group" name="group" />			
    </entity>
  </document>
</dataConfig>
```

#### 3.1.4 Upload config (edited solrconfig.xml and newly created oracle-data-config.xml)
```
bin/solr zk upconfig -z localhost:9983 -n _default -d /opt/bitnami/apache-solr/configset
Uploading /opt/bitnami/apache-solr/configset/conf for config _default to ZooKeeper at localhost:9983
```

#### 3.1.5 Verify if DataImportHandler is visible
Open in browser - http://localhost:8983/solr/#/corp-transactions/dataimport
You would see the configured data import handler here, if all went well.

## 3.2 Add fields to index
```
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"acquired_processed", "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"auth_code",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_brand",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_expiry_date",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_number",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_number_left",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_number_right",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_product_type_code",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_product_type_name",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"card_sequence_no",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"company",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"company_name",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"debit_credit",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"group",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"group_name",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"outlet_id",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"originators_transaction_ref",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"outlet_name",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"receipt_number",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"scheme",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"settlement_amount",  "type":"pdouble", "indexed":false, "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"settlement_currency",  "type":"string",  "indexed":false,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"ticket_number",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"transaction_amount",  "type":"pdouble",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"transaction_currency",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"transaction_source_name",  "type":"strings",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"transaction_type_name",  "type":"string",  "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"trading_date","type":"pdate", "indexed":true,"stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"transaction_date","type":"pdate","indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"processing_date","type":"pdate", "indexed":true,  "stored":true}}' http://localhost:8983/solr/corp-transactions/schema
```

## 3.3 Verify the schema fields
```
curl -X GET -H 'Content-type:application/json' http://localhost:8983/solr/corp-transactions/schema/fields
```

# 4. Run full import
Open in browser - http://localhost:8983/solr/#/corp-transactions/dataimport .

Select the data import handler, command as "full-import" and click on "Execute".


## Frequently used Solr commands reference

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
Delete a field
```
curl -X POST -H 'Content-type:application/json' --data-binary '{  "delete-field" : { "name":"trading_date" }}' http://localhost:8983/solr/corp-transactions/schema
```
Add a field
```
curl -X POST -H 'Content-type:application/json' --data-binary '{ "add-field": {"name":"trading_date","type":"pdate", "indexed":true,"stored":true}}' http://localhost:8983/solr/corp-transactions/schema
```
List fields in a schema
```
curl -X GET -H 'Content-type:application/json' http://localhost:8983/solr/<collection-name>/schema/fields
```
Download config
```
bin/solr zk downconfig -z localhost:9983 -n _default -d /opt/bitnami/apache-solr/configset
```
Upload config
```
bin/solr zk upconfig -z localhost:9983 -n _default -d /opt/bitnami/apache-solr/configset
```
