---
keyword: create a Hologres dimension table
---

# Create a Hologres dimension table

This topic describes how to create a Hologres dimension table. It also describes the parameters in the WITH clause, parameters in the CACHE clause, and data type mapping used when you create such a dimension table.

**Note:**

-   This topic applies only to Blink 3.6.0 and later. If your Blink version is earlier than 3.6.0, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) to obtain the required JAR files for installation.
-   We recommend that you use Hologres 0.7 or later.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and closely connected to the big data ecosystem. Hologres supports real-time analysis and processing of petabytes of data with high concurrency and low latency. This allows you to use existing Business Intelligence \(BI\) tools to easily perform multidimensional analysis and business exploration.

## Syntax

In Realtime Compute for Apache Flink, you can use a Hologres table as a dimension table. The following code shows an example:

```
CREATE TABLE rds_dim_table(
  id INT,
  len INT,
  content VARCHAR,
  PRIMARY KEY (id),
  PERIOD FOR SYSTEM_TIME  -- The identifier of a dimension table.
) with (
  type='hologres',
  endpoint='...',
  dbname='...',
  tablename='...',
  username='...',
  password='...'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the database.|Yes|Set the value to hologres.|
|endpoint|The endpoint of Hologres.|Yes|None.|
|tablename|The table from which data is read.|Yes|None.|
|dbname|The database from which data is read.|Yes|None.|
|username|The username that is used to log on to the database.|Yes|None.|
|password|The password that is used to log on to the database.|Yes|None.|

## Parameters in the CACHE clause

|Parameter|Description|Required|Example|
|---------|-----------|--------|-------|
|cache|The policy for caching data.|No|Hologres dimension tables support the following cache policies: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job starts to run, Realtime Compute for Apache Flink loads all data in the dimension table to the cache, and then searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire.

If the amount of data of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If this cache policy is used, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:**

-   If the cache parameter is set to ALL, the memory of the node for joining tables must be increased because Realtime Compute for Apache Flink asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table.
-   If the amount of data in a dimension table is large and the cache parameter is set to ALL, an out of memory \(OOM\) error may occur or a full garbage collection may be time-consuming. To address this issue, you can use one of the following methods:
    -   If the cache parameter can be set to ALL for a dimension table, enable the partitionedJoin function. For Blink versions earlier than 3.6.0, full data of the dimension table is loaded in each concurrent job by default. For Blink versions later than 3.6.0, the partitionedJoin function is supported when you set the cache parameter to ALL. After you enable the partitionedJoin function, only the required data is cached for each concurrent job.
    -   Use an HBase or ApsaraDB for RDS dimension table that uses key-value pairs to store data. |
|cacheSize|The maximum number of data records that can be cached. Unit: lines.|No|You can specify this parameter only when you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|-   You can specify this parameter only when you set the cache parameter to LRU. The cache will not expire by default.
-   If the cache parameter is set to ALL, the cacheTTLMs parameter specifies the interval at which the cache is reloaded. The cache is not reloaded by default. |
|partitionedJoin|Specifies whether to search for data by partition.|No|Valid values:-   false \(default value\)
-   true |
|async|Specifies whether to read data asynchronously.|No|Valid values:-   false \(default value\)
-   true |

## Field type mapping

|Hologres data type|Data type of Realtime Compute for Apache Flink|
|------------------|----------------------------------------------|
|INT|INT|
|INT\[\]|ARRAY<INT\>|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY<BIGINT\>|
|REAL|FLOAT|
|REAL\[\]|ARRAY<FLOAT\>|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY<DOUBLE\>|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY<BOOLEAN\>|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY<VARCHAR\>|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

## Sample code

The following sample code demonstrates how to create a Hologres dimension table in a Realtime Compute for Apache Flink job:

```
create table randomSource (a int, b VARCHAR, c VARCHAR) with (type = 'random');

create table test (
   a int, 
   b VARCHAR, 
   c VARCHAR, 
   PRIMARY KEY (a, b), PERIOD FOR SYSTEM_TIME
) with (
   type = 'hologres',
   ...
);

create table print_sink (
  a int, 
  b VARCHAR
) with (
  type = 'print', 
  `ignoreWrite` = 'false'
);

insert into print_sink
select randomSource.a, test.b from randomSource 
LEFT JOIN test FOR SYSTEM_TIME AS OF PROCTIME() 
on randomSource.a = test.a and randomSource.b = test.b;
```

