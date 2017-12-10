# Frequently used Solr commands reference

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
