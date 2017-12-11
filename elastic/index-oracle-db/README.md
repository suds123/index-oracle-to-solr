
# Configure Logstash
  - cd /etc/logstash/conf.d
  - vi trx.conf
  ```
      input{
                 jdbc{
                                jdbc_connection_string => "jdbc:oracle:thin:@host:1521:XE"
                                jdbc_user => "xxxxxx"
                                jdbc_password => "xxxxx"
                                jdbc_driver_library => "/usr/lib/ojdbc6.jar"
                                jdbc_driver_class => "Java::oracle.jdbc.driver.OracleDriver"
                                statement => "select rownum as id, mid as outlet_id, outlet_name, company, company_name, group_name, trading_date, processing_date, card_number, card_number_left, card_number_right, card_expiry_date, transaction_date, originators_transaction_ref, ticket_number, transaction_type_name, transaction_source_name, receipt_number, settlement_amount, settlement_currency, transaction_amount, transaction_currency, auth_code, card_brand, card_product_type_name, card_product_type_code,scheme, debit_credit, card_sequence_no, acquired_processed, "GROUP" from fact_transactions" 
                 }
      }
      output{
                elasticsearch{
                                "hosts" => "localhost:9200"
                                "index" => "corp-transactions"
                                "document_type" => "data"
                }
      }
```

# Run logstash
  - cd /usr/share/logstash
  - bin/logstash -f /etc/logstash/conf.d/trx.conf -w 4 -b 250


# Query
```
curl -XGET 'localhost:9200/corp-transactions/_search?pretty' -H 'Content-Type: application/json' -d'
{ "query" : { "bool": { "filter" : [ { "range": {"transaction_date" : {"from":"2016-01-01T00:00:00", "to":"2017-12-31T00:00:00"}}},{"term": { "group.keyword": "419194" }}, { "terms": { "outlet_id.keyword": ["45623133","45700493","45692263","45692763","45497633","45622743","45693493","45623133","45692263","45622743","10348833","36893313","66961433","00674985","77355914","36016999","64306926","46245277","27578361","09042215","61678619","66472571","20055895","99437999","50311108","68518855","29633355","35911472","94956196","98096335","22861216","09512581","28591395","03543491"]}} ]}}, "sort" : [ { "transaction_date" : "asc"} ],"from" : "1000", "size" : "100"}
' > q1.results
```
