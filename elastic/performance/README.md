# Query performance results
 
### 4. Data role security filter of ~2000 outlets with other search filters and fetching 100 records -
  - QTime - 600 ms
  - params - { "query" : { "bool": { "filter" : [ { "range": {"transaction_date" : {"from":"2016-01-01T00:00:00", "to":"2017-12-31T00:00:00"}}},{ "term": { "group.keyword": "419194" }}, { "terms": { "outlet_id.keyword": [<lis of MIDs]}} ]}}, "sort" : [ { "transaction_date" : "asc"} ],"from" : 1000, "size" :
 "100"}
  - numFound - 102140 (out of 27m)
  - pagination - {"start":1000, "rows":100}
  - sort - "transaction_date desc"  
  
## Conclusion
  - Faster than Solr out-of-the-box (no tuning)
  
