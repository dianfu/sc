---
keyword: [dimension table, Phoenix5]
---

# Create a Phoenix5 dimension table

This topic describes how to create a Phoenix5 dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses used when you create such a dimension table.

**Note:** Only Blink versions later than Blink 3.4.0 support Phoenix dimension tables.

## Syntax

```
create table US_POPULATION_DIM (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY),
  PERIOD FOR SYSTEM_TIME
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<YourServerUrl>',
  tableName = '<YourTableName>'
);           
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the dimension table.|Yes|Set the value to `PHOENIX5`.|
|serverUrl|The address of the Phoenix5 Query Server. -   If Phoenix5 is created in a cluster, the URL of Server Load Balancer \(SLB\) is used.
-   If Phoenix5 is created on a server, the URL of the server is used.

|Yes|You must enable the HBase SQL service in an ApsaraDB for HBase instance.|
|tableName|The name of the Phoenix5 table.|Yes|None.|

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The policy for caching data.|No|Valid values: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job starts to run, the system loads all data in the dimension table to the cache, and then searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire.

If the amount of data of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If this cache policy is used, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:** If the cache parameter is set to ALL, the memory of the node for joining tables must be increased because Realtime Compute for Apache Flink asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The maximum number of data records that can be cached. Unit: lines.|No|If you set the cache parameter to LRU, you can set this parameter to specify the maximum number of data records that can be cached. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|If the cache parameter is set to LRU, the cacheTTLMs parameter specifies the time allowed before cache entries expire. Cache entries do not expire by default. If the cache parameter is set to ALL, the cacheTTLMs parameter specifies the interval at which the cache is loaded. The cache is not reloaded by default.|
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for massive online promotional events such as Double 11.|No|This parameter is optional. It is empty by default. Example: '2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'. Separate multiple time periods with commas \(,\). Separate the start time and end time of each time period with a hyphen and a greater-than sign \(-\>\).|

## Sample code

```
CREATE TABLE datahub_input1 (
  id  BIGINT,
  name  VARCHAR,
  age   BIGINT
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
  t.id,
  w.phoneNumber,
  t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w -- You must include this clause in the INSERT INTO statement when you join a dimension table with another table.
ON t.name = w.name;
```

