## Terms lookup mechanism

When it’s needed to specify a terms filter with a lot of terms it can be beneficial to fetch those term values from a document in an index. A concrete example would be to filter tweets tweeted by your followers. Potentially the amount of user ids specified in the terms filter can be a lot. In this scenario it makes sense to use the terms filter’s terms lookup mechanism.

The terms lookup mechanism supports the following options:

index

The index to fetch the term values from.

type

The type to fetch the term values from.

id

The id of the document to fetch the term values from.

path

The field specified as path to fetch the actual values for the terms filter.

routing

A custom routing value to be used when retrieving the external terms doc.


# Customizing Field Mappings
- **not_analyzed:** Index this field so it is searchable, but index the value exactly as specified. Do not analyze it.
- **no:** Don’t index this field at all. This field will not be searchable.
The default value of index for a string field is analyzed. If we want to map the field as an exact value, we need to set it to not_analyzed:

{
    "tag": {
        "type":     "string",
        "index":    "not_analyzed"
    }
}
# Take Notice of refresh_interval
The update settings API can be used to dynamically change the index from being more performant for bulk indexing, and then move it to more real time indexing state. Before the bulk indexing is started, use:

curl -XPUT 'localhost:9200/test/_settings' -d '{
    "index" : {
        "refresh_interval" : "-1"
    }
}'

If you are doing a large bulk import, consider disabling replicas by setting index.number_of_replicas: 0. If you index with zero replicas and then enable replicas when ingestion is finished, the recovery process is essentially a byte-for-byte network transfer. This is much more efficient than duplicating the indexing process.
curl -XPUT 'localhost:9200/my_index/_settings' -d ' {
    "index" : {
        "number_of_replicas" : 0
    }
}'

Then, once bulk indexing is done, the settings can be updated (back to the defaults for example):

curl -XPUT 'localhost:9200/my_index/_settings' -d '{
    "index" : {
        "refresh_interval" : "1s"
    } 
}'
And, a force merge should be called:

curl -XPOST 'localhost:9200/my_index/_forcemerge?max_num_segments=5'

curl -XPOST 'localhost:9200/my_index/_refresh'

# Segments and Merging
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
    "persistent" : {
        "indices.store.throttle.max_bytes_per_sec" : "100mb"
    }
}'
OR disable
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "indices.store.throttle.type" : "none" 
    }
}'

reenable throttling.

curl -XPUT 'localhost:9200/_cluster/settings' -d '{
    "transient" : {
        "indices.store.throttle.type" : "merge" 
    }
}'
# Disable Norms for Analyzed Fields
curl -XPUT ‘http://localhost:9200/my_index/_mapping/my_type’ -d ‘{
  "properties": {
    "title": {
      "type": "string",
      "norms": {
        "enabled": false
      }
    }
  }
}’

# Disabling the _all Field

curl -XPUT ‘http://localhost:9200/my_index’ -d ‘{
  "mappings": {
    "type_1": {
      "properties": {...}
    },
    "type_2": {
      "_all": {
        "enabled": false
      },
      "properties": {...}
    }
  }
}’

# Beware of Default index_options
Set index_options to what you really need, as outlined in the string core type description.
**docs:** Only the doc number is indexed. Can answer the question Does this term exist in this field?

curl -XPUT ‘http://localhost:9200/my_index’ -d ‘{
  "mappings": {
    "my_type": {
      "properties": {
        "text": {
          "type": "text",
          "index_options": "offsets"
        }
      }
    }
  }
}’

