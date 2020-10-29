# Create an AnalyticDB for MySQL V3.0 dimension table

This topic describes how to create an AnalyticDB for MySQL V3.0 dimension table. It also describes parameters in the WITH and CACHE clauses used when you create such a dimension table.

**Note:** This topic applies only to Blink-3.5.0-hotfix and later.

## Syntax

```
CREATE TABLE dim_ads(
    `name` VARCHAR,
    id VARCHAR,
    PRIMARY KEY (`name`),
    PERIOD FOR SYSTEM_TIME
)with(
    type='ADB30',
    url='jdbc:mysql://<Internal network address>/<databaseName>',
    tableName='xxx',
    userName='xxx',
    password='xxx'
);
```

**Note:**

-   You must specify a primary key when you declare a dimension table.
-   When you join a dimension table with another dimension table, the ON condition must contain equivalent conditions that include all primary keys.
-   In AnalyticDB for MySQL, you can use the primary key or unique index of a dimension table as the primary key.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the dimension table.|Yes|Set the value to ADB30.|
|url|The URL of the AnalyticDB for MySQL database.|Yes|The URL of the AnalyticDB for MySQL database, for example, `url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`.**Note:**

-   For more information about how to access an AnalyticDB for MySQL database, see [Register an AnalyticDB for MySQL instance](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an AnalyticDB for MySQL instance.md).
-   databaseName: the name of the AnalyticDB for MySQL database. |
|tableName|The name of the table.|Yes|None.|
|userName|The username of the database user.|Yes|None.|
|password|The password of the database user.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data to the table.|No|Default value: 3.|

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|AnalyticDB for MySQL V3.0 supports the following cache policies: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job starts to run, the system loads all data in the dimension table to the cache, and then searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire.

If the amount of data of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If you use this cache policy, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:**

-   If you set the cache parameter is to ALL, you must increase the memory of the node for joining tables because Realtime Compute for Apache Flink asynchronously loads data from the dimension table. The increased memory size is twice the memory size of the remote table.
-   If the amount of data in a dimension table is large and the cache parameter is set to ALL, an out of memory \(OOM\) error may occur or a full garbage collection may be time-consuming. To address this issue, you can use one of the following methods:
    -   If the cache parameter can be set to ALL for a dimension table, enable the partitionedJoin function. If you use a Blink version earlier than 3.6.0, full data of the dimension table is loaded for each concurrent job by default. If you use a Blink version later than 3.6.0, you can enable the partitionedJoin function when the cache parameter is set to ALL. After you enable the partitionedJoin function, only the required data is cached for each concurrent job.
    -   Use an HBase or ApsaraDB RDS dimension table that uses key-value pairs to store data. |
|cacheSize|The cache size. Unit: lines.|You can set this parameter when the cache parameter is set to LRU. Default value: 10000.|
|cacheTTLMs|The cache refresh interval. The system reloads the latest data in the dimension table based on the value of this parameter to ensure that the source table can be joined with the latest data of the dimension table.|Unit: milliseconds. This parameter is empty by default, which indicates that the latest data in the dimension table is not reloaded.|
|cacheReloadTimeBlackList|The time periods during which cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for massive online promotional events such as Double 11.|This parameter is optional. It is empty by default. Example: '2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'. Make sure that the parameter conforms to the following rules:-   Separate multiple time periods with commas \(,\).
-   Separate the start time and end time of each time period with a hyphen and a greater-than sign \(-\>\). |
|partitionedJoin|Specifies whether to enable partitionedJoin. If you enable the partitionedJoin function, the shuffle operation is performed before the source table is associated with the dimension table based on join keys. -   If you set the cache parameter to LRU, the cache hit rate increases.
-   If you set the cache parameter to ALL, you can save memory resources because only the required data is cached for each concurrent job.

|This parameter is set to false by default, which indicates that the partitionedJoin function is disabled. **Note:** Before you enable the partitionedJoin function, set partitionedJoin to true. |

## Sample code

```
CREATE TABLE datahub_input1 (
  id      BIGINT,
  name    VARCHAR,
  age     BIGINT
) WITH (
  type='datahub'
);

create table phoneNumber (
  name VARCHAR,
  phoneNumber BIGINT,
  primary key(name),
  PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) with (
  type='ADB30'
);

CREATE table result_infor (
  id BIGINT,
  phoneNumber BIGINT,
  name VARCHAR
) with (
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

