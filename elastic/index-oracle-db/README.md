
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
