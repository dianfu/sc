---
keyword: [HBase, dimension table]
---

# Create an ApsaraDB for HBase dimension table

This topic describes how to create an ApsaraDB for HBase dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses used when you create such a dimension table.

**Note:**

-   Blink versions earlier than Blink 3.3.0 support only HBase Standard Edition.
-   Blink 3.3.0 and later versions support HBase Standard Edition and HBase Enhanced Edition.
-   Blink 3.5.0 and later versions support switchover between primary and secondary ApsaraDB for HBase databases for data writing.
-   For more information about the JOIN syntax of an ApsaraDB for HBase dimension table, see [JOIN \(for dimension tables\)](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/Dimension table JOIN statement.md).
-   ApsaraDB for HBase dimension tables in Realtime Compute for Apache Flink do not support user-created open source HBase.
-   Only one primary key is allowed in an ApsaraDB for HBase dimension table.

## DDL syntax

-   HBase Standard Edition

    ```
    CREATE TABLE hbase (
      `key` varchar, 
      `name` varchar,
      PRIMARY KEY (`key`), -- The rowkey field of the ApsaraDB for HBase dimension table.
      PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
    ) with (
      TYPE = 'cloudhbase',
      zkQuorum = '<yourzkQuorum>',
      columnFamily = '<yourColumnFamilyName>',
      tableName = '<yourTableName>'
    );
    ```

-   HBase Enhanced Edition

    ```
    CREATE TABLE hbase (
      `key` varchar, 
      `name` varchar,
      PRIMARY KEY (`key`), -- The rowkey field of the ApsaraDB for HBase dimension table.
      PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
    ) with (
      TYPE = 'cloudhbase',
      endPoint = '<host:port>',-- The Java API URL that is used to access the Enhanced Edition of an ApsaraDB for HBase database.
      userName  = 'root', -- The username that is used to access an ApsaraDB for HBase database.
      password = 'root', -- The password that is used to access an ApsaraDB for HBase database.
      columnFamily = '<yourColumnFamilyName>',
      tableName = '<yourTableName>'
    );
    ```

-   HBase Enhanced Edition for Blink 3.5.0 and later

    ```
    create table liuxd_user_behavior_test_front (
      row_key varchar,
      from_topic varchar,
      origin_data varchar,
      record_create_time varchar,
      primary key (row_key)
    ) with (
      type = 'cloudhbase',
      zkQuorum = '<host:port>', -- The Java API URL that is used to access the Enhanced Edition of an ApsaraDB for HBase database.
      userName  = 'root', -- The username that is used to access an ApsaraDB for HBase database.
      password = 'root', -- The password that is used to access an ApsaraDB for HBase database.
      columnFamily = '<yourColumnFamily>',
      tableName = '<yourTableName>',
      batchSize = '500'
    );
    ```

-   Blink 3.5.0 and later versions support switchover between primary and secondary ApsaraDB for HBase databases for data writing.

    ```
    create table liuxd_user_behavior_test_front (
      row_key varchar,
      from_topic varchar,
      origin_data varchar,
      record_create_time varchar,
      primary key (row_key)
    ) with (
      type = 'cloudhbase',
      zkQuorum = '<host:port>', -- The URL that is used to access ApsaraDB for HBase databases in high availability (HA) mode.
      haClusterID = 'ha-xxx', -- The instance ID of ApsaraDB for HBase databases in HA mode.
      userName  = 'root', -- The username that is used to access an ApsaraDB for HBase database.
      password = 'root', -- The password that is used to access an ApsaraDB for HBase database.
      columnFamily = '<yourColumnFamily>',
      tableName = '<yourTableName>',
      batchSize = '500' 
    );
    ```


**Note:**

-   When you declare a dimension table, you must specify a primary key.
-   When you join a dimension table with another dimension table, the ON condition must contain equivalent conditions that include all primary keys. The primary key in the preceding examples is **row\_key**.
-   The connection parameters in HBase Standard Edition and HBase Enhanced Edition are different.
    -   HBase Standard Edition: `zkQuorum`.
    -   HBase Enhanced Edition: `endPoint`.
    -   HBase Standard Edition and HBase Enhanced Edition for Blink 3.5.0 and later: `zkQuorum`.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the dimension table.|Yes|Set the value to `cloudhbase`.|
|zkQuorum|The ZooKeeper address configured for the ApsaraDB for HBase cluster. The address is a list of hosts separated by commas \(,\).|Yes|You can view the configuration related to hbase.zookeeper.quorum in the hbase-site.xml file. **Note:** This parameter takes effect only in HBase Standard Edition. |
|zkNodeParent|The path of the cluster configured on the ZooKeeper servers.|No|You can view the configuration related to hbase.zookeeper.quorum in the hbase-site.xml file. **Note:** This parameter takes effect only in HBase Standard Edition. |
|endPoint|The name of the region where your ApsaraDB for HBase instance is deployed.|Yes|You can obtain the value of this parameter from the console of your ApsaraDB for HBase instance. **Note:** This parameter takes effect only in HBase Enhanced Edition. |
|userName|The username that is used to log on to the ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in HBase Enhanced Edition. |
|password|The password that is used to log on to the ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in HBase Enhanced Edition. |
|tableName|The name of the ApsaraDB for HBase dimension table.|Yes|None.|
|columnFamily|The column family name.|Yes|Only the same column family can be inserted.|
|maxRetryTimes|The maximum number of retries for writing data into the table.|No|Default value: 10.|
|partitionedJoin|Specifies whether to use joinKey for partitioning.|No|Default value: False. If you set this parameter to True, joinKey is used for partitioning. Data is delivered to each node for joining, which increases the cache hit ratio.|
|shuffleEmptyKey|Specifies whether to randomly send upstream empty keys to downstream nodes.|No|Default value: True. Valid values: -   True: If multiple empty keys exist in the upstream, Realtime Compute for Apache Flink randomly sends all the empty keys to each JOIN node.
-   False: If multiple empty keys exist in the upstream, Realtime Compute for Apache Flink sends all the empty keys to a single JOIN node.

**Note:** This parameter can be used only after the partitionedJoin parameter takes effect. |

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The policy for caching data.|No|Valid values: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job starts to run, Realtime Compute for Apache Flink loads all data in the dimension table to the cache, and then searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire.

If the amount of data of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If this cache policy is used, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:** If the cache parameter is set to ALL, the memory of the node for joining tables must be increased because Realtime Compute for Apache Flink asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The maximum number of data records that can be cached. Unit: lines.|No|You can set this parameter when the cache parameter is set to `LRU`. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|If the cache parameter is set to `LRU`, the cacheTTLMs parameter specifies the time before cache entries expire. Cache entries do not expire by default. If the cache parameter is set to `ALL`, the cacheTTLMs parameter specifies the interval at which the cache is loaded. The cache is not reloaded by default.|
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter is used when the cache parameter is set to `ALL`. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for massive online promotional events such as Double 11.|No|This parameter is empty by default. Custom input format: ```
2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00
```

Separate multiple time periods with commas \(,\). Separate the start time and end time of each time period with a hyphen and a greater-than sign \(-\>\).|
|cacheScanLimit|The maximum number of rows returned by the server to the client for each remote procedure call \(RPC\) when the server reads full ApsaraDB for HBase data. This parameter is used when the cache parameter is set to `ALL`. .|No|Default value: 100.|

## Sample code

The following sample code demonstrates how to create an ApsaraDB for HBase dimension table in a Realtime Compute for Apache Flink job.

```
create table source (
  id   TINYINT,
  name BIGINT
) with (
  type = 'random'
);

create table dim (
  id   TINYINT,
  score BIGINT
  primary key(id),
  PERIOD FOR SYSTEM_TIME
)with(
  type = 'cloudhbase',
  zkQuorum = '<yourzkQuorum>',
  columnFamily = '<yourColumnFamilyName>',
  tableName = '<yourTableName>'
);

CREATE table result_infor(
  id BIGINT,
  score BIGINT
)with(
  type='rds'
);

INSERT INTO result_infor
SELECT
  t.id,
  w.score
FROM source as t
JOIN dim FOR SYSTEM_TIME AS OF PROCTIME() as w
ON t.id = w.id;
```

