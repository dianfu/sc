# Create an Elasticsearch dimension table

This topic describes how to create an Elasticsearch dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses used when you create an Elasticsearch dimension table.

**Note:** This topic applies only to Blink 3.2.2 and later.

## DDL syntax

In Realtime Compute for Apache Flink, you can use an Elasticsearch table as a dimension table. The following code shows an example:

```
 CREATE TABLE es_stream_sink(
  field1 LONG, 
  field2 VARBINARY, 
  field3 VARCHAR,
  PRIMARY KEY(field1),
  PERIOD FOR SYSTEM_TIME
) WITH (
  type ='elasticsearch',
  endPoint = '<yourEndPoint>',
  accessId = '<yourAccessId>',
  accessKey = '<yourAccessSecret>',
  index = '<yourIndex>',
  typeName = '<yourTypeName>'
);
```

**Note:** You can update data in Elasticsearch tables based on the primary keys of the tables. You can specify only one field for each primary key.

## Parameters in the WITH clause

|Parameter|Description|Default value|Required|
|---------|-----------|-------------|--------|
|type|The type of the dimension table.|elasticsearch|Yes|
|endPoint|The IP address of the server for the Elasticsearch cluster. For example, you can set this parameter to http://127.0.0.1:9211.|None|Yes|
|accessId|The AccessKey ID that is used to access an Elasticsearch instance.|None|Yes|
|accessKey|The AccessKey secret that is used to access an Elasticsearch instance.|None|Yes|
|index|The index name, which is similar to the database name.|None|Yes|
|typeName|The type name, which is similar to the database table name.|None|Yes|
|maxRetryTimes|The maximum number of retries for writing data into the table.|30|No|
|timeout|The read timeout period. Unit: milliseconds.|600000|No|
|discovery|Specifies whether node discovery is enabled. If this feature is enabled, the client refreshes the server list every five minutes.|false|No|
|compression|Specifies whether to compress request bodies in the GZIP format.|true|No|
|multiThread|Specifies whether to enable multithreading for JestClient.|true|No|

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|-   None \(default value\): indicates that no data is cached.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all the data in the dimension table is cached. Before Realtime Compute for Apache Flink runs a job, Realtime Compute for Apache Flink loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, Realtime Compute for Apache Flink searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire. |
|cacheSize|The cache size.|This parameter is available only if you set the cache parameter to `LRU`. Default value: 10000.|
|cacheTTLMs|The interval at which the cache is refreshed.|The cache does not time out by default. The purpose of setting this parameter varies based on the cache policy. -   If the cache parameter is set to LRU, the cacheTTLMs parameter specifies the timeout period of the cache.
-   If the cache parameter is set to ALL, the cacheTTLMs parameter specifies the interval at which the cache is refreshed. The cache is not refreshed by default. |

