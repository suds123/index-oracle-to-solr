
# Solr Sizing
1. Load sample data. 
   - "numDocs":27046444
   - "size": 4.47 GB
   - "Per 1m docs": 0.16527 GB
   - "Per 1b docs" -> 165.27 GB
3. Assume 1GB of memory for the OS and 14 GB of memory for the JVM heap, or an overhead of 15GB. 
4. Sizing Equation -> ((Nodes * Memory Per Node) - (15GB * Nodes)) / (1b Index Size * #billion docs)
   - ((5*512 GB) - (15GB * 5)) / (165.27 * 100) => 1205/165.27 => ~1.5
   - If the value is less than 1 then you can expect problems. You won’t have enough memory to cache the index, let alone cache the rows. Every query will hit the disk multiple times

# Tuning

1. Hardware
   - Provide as much memory as you can afford
   - Use SSDs if possible
   - If using mechanical disks, setup at least 4 volumes with a set of dedicated heads for each: OS, commit log, SSTables, Solr data 
   - If using SSDs you can choose either RAID0 or JBOD
   - Additional cores are helpful because it will increase query/indexing throughput
   
2. JVM Heap 
   - Set the heap to 14G; Set the new heap to 100mb per core with a max of 800mb; 

3. Row Cache & Key Cache 
   - Disable the row cache; Leave the key cache at default values unless load testing dictates otherwise;

# SolrConfig.xml Recommendations
1. Soft Commit Threshold - 
   - Set the soft autocommit max time to 10s. This is the maximum amount of time between a document being inserted and it entering the queue to be indexed. Only after it’s been indexed will the document become visible to queries
   - If you can tolerate a larger value feel free to increase
   - This is one of the most effective knobs you have

2. Filter Cache
   - The filter cache is the only meaningful cache, all others should be disabled
   - Start with a filter cache setting of 256; 512 is often too big for a 14G heap and will cause GC pressure
   - Don’t enable auto warming unless you have frequently used filters

3. Document and Query Cache
   - Document and query result caches are disabled. They were flushed with every soft commit so they were mostly useless except for read-only use cases
   
4. Merge Factor
   - Merge factor of 10. This is a good compromise value, for read only use cases you might lower this value to 5 or 2, for write heavy or balanced use cases leave it at 10
   
5. Lucene Version
   - Set the Lucene version to the most recent version supported by the release
   - If this value is changed the index should be rebuilt
   
6. Type Mapping
   - Set the type mapping to the most recent version supported by the release
   - If this value is changed the Solr metadata must be removed from the CF, and Solr must be re-enabled
   - Don’t ever change this value if at all possible   
   
# Schema.xml Recommendations

1. Schema Version
   - The schema version is defined in the root node of the XML document 
   - Avoid specifying a schema version if possible
   - if you specify an older version it will most likely result in undesirable behavior 
   - For example, if you specify “1.0” all fields are multi-valued by default

2. Dynamic Fields
   - Avoid or limit the use of dynamic fields
   - Lucene allocates memory for each unique field (column) name
   - This means if you have a row with columns A,B,C, and another row with B, D, E, Lucene will allocate 5 chunks of memory
   - Do this for millions of rows, and it’s fairly easy to blow the heap
   - CopyField Directive: Instead of using dynamic fields you can copy field contents using the CopyField directive, and then perform queries against the combined field
   - FieldInputTransformer: In the same vein as the CopyField directive there is an interface that you can implement which allows for the interception of a document (row), prior to indexing and rewrite columns   

# Troubleshooting

1. GC pressure, frequent garbage collections
   - Heap is too small, or caches are too big. See recommended setup

2. Slow Queries
   - Slow is relative. Results in 20ms or less is considered good. Less than 10ms is very good. Under load with large datasets it’s not uncommon to see latencies of 100ms to 200ms.
   - Common causes for slow queries include:
     - Disk/file system is laid out incorrectly
     - Not enough memory to cache Solr index files and hot dataset
     - Check IOwait metrics in top/vmstat/iostat.
     - GC pressure can result in high latency variance
     - Double check your queries

3. Poor Indexing Performance
   - If you have small document (row) sizes, have a high indexing throughput use case, and experience a slowdown in indexing performance over time then decrease the SSTable size 
     - After a document is indexed it goes into the docsPending queue, and is eventually flushed when the memtable is flushed to disk
     - If the documents are small, then the time between flushes for an SSTable can be large causing the docsPending queue to grow resulting in an indexing performance degradation

4. Slow Startup Times
   - Decrease the commit log/sstable size as Data replayed via the commit log must be re-indexed

5. Inconsistent Query Results
   - If you are seeing data inconsistency be sure to run repairs regularly (within GC grace)

6. Dropped Mutations
   - If you are seeing consistently dropped mutations and you've followed all other recommendations here, the system load is probably too high for the hardware/cluster size. Upgrading hardware or adding additional nodes will alleviate dropped mutations

7. Read/Socket Timeout
   - Decrease max_solr_concurrency_per_core in dse.yaml to 1 per CPU core

8. Out Memory Errors
   - There can be several root causes to this problem
   - The most common are: 
     - heap size set too low
     - cache sizes set too large
     - heavy use of dynamic fields


