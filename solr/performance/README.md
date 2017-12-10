
# Query performance results

### 1. Data role security filter of ~2000 outlets with no other search filters -
  - QTime - 530 ms
  - params - {"q":"*:*", "fq":"{!terms f=outlet_id}<list of comma separated outlet-ids>"}
  - numFound - 290749 (out of 27m)
  - pagination - {"start":0, "rows":10}

### 2. Data role security filter of ~2000 outlets with other search filters and fetching 1000 records -
  - QTime - 2433 ms 
  - params - {"q":"*:*", "fq":["{!terms f=outlet_id}<list of comma separated outlet-ids>", "transaction_currency:\"GBP\"",        "transaction_date:[2015-01-01T00:00:00Z TO NOW]", "transaction_amount:[0 TO 1000]", "group:\"419194\"", "scheme:\"MasterCard\""]
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":1000}
  - sort - "transaction_date desc"
  
### 3. Data role security filter of ~2000 outlets with other search filters and fetching 2000 records -
  - QTime - 3076 ms
  - params - {"q":"*:*", "fq":["{!terms f=outlet_id}<list of comma separated outlet-ids>", "transaction_currency:\"GBP\"",        "transaction_date:[2015-01-01T00:00:00Z TO NOW]", "transaction_amount:[0 TO 1000]", "group:\"419194\"", "scheme:\"MasterCard\""]
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":2000}
  - sort - "transaction_date desc"
  
### 4. Data role security filter of ~2000 outlets with other search filters and fetching 100 records -
  - QTime - 2128 ms
  - params - {"q":"*:*", "fq":["{!terms f=outlet_id}<list of comma separated outlet-ids>", "transaction_currency:\"GBP\"",        "transaction_date:[2015-01-01T00:00:00Z TO NOW]", "transaction_amount:[0 TO 1000]", "group:\"419194\"", "scheme:\"MasterCard\""]
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":100}
  - sort - "transaction_date desc"  
  
### 5. Data role security filter of ~10 outlets with other search filters and fetching 100 records -
  - QTime - 923 ms
  - params - {"q":"*:*", "fq":["{!terms f=outlet_id}<list of comma separated outlet-ids>", "transaction_currency:\"GBP\"",        "transaction_date:[2015-01-01T00:00:00Z TO NOW]", "transaction_amount:[0 TO 1000]", "group:\"419194\"", "scheme:\"MasterCard\""]
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":100}
  - sort - "transaction_date desc"  
  
### 6. Data role security filter of ~10 outlets with other search filters, fetching 100 records and no sorting -
  - QTime - 1015 ms
  - params - {"q":"*:*", "fq":["{!terms f=outlet_id}<list of comma separated outlet-ids>", "transaction_currency:\"GBP\"",        "transaction_date:[2015-01-01T00:00:00Z TO NOW]", "transaction_amount:[0 TO 1000]", "group:\"419194\"", "scheme:\"MasterCard\""]
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":100}

## Conclusion
  - no. of terms in a filter query is a major factor
  - sorting doesn't seem to have any affect (more tests required)
  - no. of rows paginated is a minor factor as expected
