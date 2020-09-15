# Create a Phoenix5 dimension table

This topic describes how to create a Phoenix5 dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses used when you create such a dimension table.

**Note:** Only Blink versions later than Blink 3.4.0 support Phoenix dimension tables.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|serverUrl|The address of the Phoenix5 Query Server. -   If Phoenix5 is created in a cluster, the URL of Server Load Balancer \(SLB\) is used.
-   If Phoenix5 is created on a server, the URL of the server is used.

|Yes|You must enable the HBase SQL service in the ApsaraDB for HBase console. For more information.|
|tableName|The name of the Phoenix5 table.|Yes|None.|

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The policy for caching data.|No|Valid values: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job starts to run, Realtime Compute for Apache Flink loads all data in the dimension table to its cache, and then searches the cache for all subsequent queries on the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

We recommend that you set this parameter to ALL if the data volume of a remote table is small and a large number of missing keys exist. \(The source table and dimension table cannot be associated based on the ON clause.\)

If this cache policy is used, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:** If the cache parameter is set to ALL, the memory of the node for joining tables must be increased because Realtime Compute for Apache Flink asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The maximum number of data records that can be cached. Unit: lines.|No|You can set this parameter if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|If the cache parameter is set to LRU, the cacheTTLMs parameter specifies the time before cache entries expire. Cache entries do not expire by default. If the cache parameter is set to ALL, the cacheTTLMs parameter specifies the cache refresh interval. The cache is not refreshed by default.|
|cacheReloadTimeBlackList|The time periods during which cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. During the time periods specified by this parameter, the cache is not refreshed \(for example, in Double 11\).|No|Optional. This parameter is empty by default. Example: '2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'. Separate multiple time periods with commas \(,\). Separate the start time and end time of each time period with a hyphen and a greater-than sign \(-\>\).|

## Sample code

```
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

create table phoneNumber(
name VARCHAR,
phoneNumber BIGINT,
primary key(name),
PERIOD FOR SYSTEM_TIME-- The identifier of a dimension table.
)with(
type='PHOENIX5'
);

CREATE table result_infor(
id BIGINT,
phoneNumber BIGINT,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id
,w.phoneNumber
,t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w -- You must include this clause in the INSERT INTO statement if you are performing a JOIN query for a dimension table.
ON t.name = w.name;
```

