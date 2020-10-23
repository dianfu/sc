---
keyword: [cache, cache policy]
---

# Cache policy

When you join two dimension tables, you can configure a cache policy to improve the job throughput.

## Background information

In Realtime Compute for Apache Flink, you can set the cache parameter to specify a cache policy:

-   If the cache parameter is set to LRU, some data in the dimension table is cached. The system creates a local LRU cache for each join node. Realtime Compute for Apache Flink searches for data in the cache each time it reads a data record in the source table. The data that meets the requirement is returned. This reduces I/O requests. If no data in the cache meets the requirement, Realtime Compute for Apache Flink searches the dimension table. The data that meets the requirement is stored in the cache for subsequent queries.

    To limit the volume of data that can be stored in the cache, you can set cacheSize. To regularly update the data of a dimension table, you can set cacheTTLMs to adjust the cache expiration time. cacheTTLMs takes effect for all cached data records. If a cached data record is not accessed within the specified period of time, it is removed from the cache.

-   If the cache parameter is set to All, all data in the dimension table is cached. The system creates an asynchronous thread for the join node to synchronize data between the cache and dimension table. The input data is blocked from the moment a job is started to the moment loading data to the cache is completed. This ensures that the data in the dimension table is loaded to the cache before input data processing starts.

    Realtime Compute for Apache Flink searches the cache in subsequent dimension table queries. If the data that meet the requirement cannot be found in the cache, the join key does not exist. If data in the cache expires, Realtime Compute for Apache Flink reloads the data in the dimension table to the cache. The reloading process does not affect the join operation of dimension tables. The reloaded data is stored in the temporary memory. The atomic substitution operation is performed after all data in the dimension table is reloaded.

    If cache is set to ALL, the join operation of dimension tables can achieve excellent performance because few I/O requests are initiated. However, the memory must be large enough to store the data of two dimension tables.


**Note:** Do not specify the cache policy if data in the cache is not allowed for each dimension table query.

## Optimization method

Add cache='LRU' or cache='ALL' to the WITH clause in the DDL statement of the dimension table. The following table describes the parameters related to the cache policy.

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|-   None: indicates that data is not cached. This is the default value.
-   LRU: If you set this parameter to LRU, you must configure cacheSize, cacheTTLMs, and partitionedJoin.
-   ALL: If you set this parameter to ALL, you must configure cacheTTLMs, cacheReloadTimeBlackList, and partitionedJoin. |
|cacheSize|The cache size. Unit: rows.|No|You can set this parameter only after you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache expiration period or the cache reloading interval. Unit: milliseconds.|No|-   If you set the cache parameter to LRU, this parameter specifies the cache expiration period. The cache does not expire by default.
-   If you set the cache parameter to ALL, this parameter specifies the cache reloading interval. The cache is not reloaded by default. |
|cacheReloadTimeBlackList|The periods during which cache reloading is not allowed. This parameter takes effect if the cache parameter is set to ALL. During the periods specified by this parameter, the cache is not reloaded \(for example, in Double 11\).|No|Optional. This parameter is empty by default. Example: '2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'. -   Separate multiple periods with commas \(,\).
-   Separate the start time and end time of each period with a hyphen and a greater-than sign \(-\>\). |
|partitionedJoin|Specifies whether to enable Partitioned All Cache.|No|By default, this parameter is set to false, which indicates that Partitioned All Cache is disabled. If Partitioned All Cache is enabled, data is shuffled before the source table is associated with the dimension table based on join keys. -   If the cache parameter is set to LRU, the cache hit rate increases.
-   If the cache parameter is set to ALL, memory consumption is reduced because only the required data is cached for each concurrent job. |

## Sample code

```
 CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id)
) with (
  type = 'odps',
  endPoint = 'your_end_point_name',
  project = 'your_project_name',
  tableName = 'your_table_name',
  accessId = 'your_access_id',
  accessKey = 'your_access_key',
  `partition` = 'ds=20180905', 
  cache = 'ALL'
);
```

