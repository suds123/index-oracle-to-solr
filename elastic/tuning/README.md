Elasticsearch nodes come under 4 flavors:
 - (Eligible) master nodes: controls the cluster.
 - Http nodes: to run your queries to.
 - Data nodes: the place data is stored, obviously.
 - Coordinating nodes: see them as smart load balancers.
 
The minimum requirement for a fault tolerant cluster is:
 - 3 locations to host your nodes. 2 locations to run half of your cluster, and one for the backup master node.
 - 3 master nodes. You need an odd number of eligible master nodes to avoid split brains when you lose a whole data center. Put one master node in each location so you hopefully never lose the quorum.
 - 2 http nodes, one in each primary data center.
 - As many data nodes as you need, split evenly between both main locations.
 
Elasticsearch provides an interesting feature called shard allocation awareness. It allows to split the primary shards and their replica in separated zones. Allocate nodes within a same data center to a same zone to limit the odds of having your cluster go red.
```
cluster.routing.allocation.awareness.attributes: "rack_id"
node.attr.rack_id: "dontsmokecrack"
```
Using rack_id on the http nodes is interesting too, as Elasticsearch will run the queries on the closest neighbours. A query sent to the http node located in the datacenter 1 will more likely run on the same data center data nodes.

# Lucene segments
Each Elasticsearch index is divided into shards. Shards are both logical and physical division of an index. Each Elasticsearch shard is a Lucene index. The maximum number of documents you can have in a Lucene index is 2,147,483,519. The Lucene index is divided into smaller files called segments. A segment is a small Lucene index. Lucene searches in all segments sequentially.

Lucene creates a segment when a new writer is opened, and when a writer commits or is closed. It means segments are immutable. When you add new documents into your Elasticsearch index, Lucene creates a new segment and writes it. Lucene can also create more segments when the indexing throughput is important.
From time to time, Lucene merges smaller segments into a larger one. the merge can also be triggered manually from the Elasticsearch API.
This behavior has a few consequences from an operational point of view.
The more segments you have, the slower the search. This is because Lucene has to search through all the segments in sequence, not in parallel. Having a little number of segments improves search performances.
Lucene merges have a cost in terms of CPU and I/Os. It means they might slow your indexing down. When performing a bulk indexing, for example an initial indexing, it is recommended to disable the merges completely.
If you plan to host lots of shards and segments on the same host, you might choose a filesystem that copes well with lots of small files and does not have an important inode limitation. This is something we’ll deal in details in the part about choosing the right file system.

Elasticsearch default index buffer is 10% of the memory allocated to the heap. But for heavy indexing operations, you might want to raise it to 30%, if not 40%.
indices.memory.index_buffer_size: 40%

index.refresh_interval: "1m"

 
 
