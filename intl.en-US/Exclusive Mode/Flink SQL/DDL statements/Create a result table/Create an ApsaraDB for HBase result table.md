---
keyword: [HBase, result table]
---

# Create an ApsaraDB for HBase result table

This topic describes how to create an ApsaraDB for HBase result table in Realtime Compute for Apache Flink.

**Note:**

-   This topic applies only to Realtime Compute for Apache Flink in exclusive mode.
-   Blink versions earlier than 3.3.0 support only HBase Standard Edition.
-   Blink 3.3.0 and later versions support both HBase Standard Edition and HBase Enhanced Edition.
-   Blink 3.5.0 and later versions support switchover between primary and secondary ApsaraDB for HBase databases for data writing.
-   ApsaraDB for HBase result tables in Realtime Compute for Apache Flink do not support self-managed open source HBase.

## DDL syntax

In Realtime Compute for Apache Flink, you can use ApsaraDB for HBase to store output data.

-   The following sample code demonstrates how to create an ApsaraDB for HBase result table of HBase Standard Edition:

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        zkQuorum = '2',  
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500'
    );
    ```

-   The following sample code demonstrates how to create an ApsaraDB for HBase result table of HBase Enhanced Edition:

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        endPoint = '<host:port>', ----The Java API URL that is used to access the Enhanced Edition of an ApsaraDB for HBase database.
        userName  = 'root', -- The username that is used to access the ApsaraDB for HBase database.
        password = 'root', --The password that is used to access the ApsaraDB for HBase database.
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500'
    );
    ```

-   The following sample code demonstrates how to create an ApsaraDB for HBase dimension table of HBase Enhanced Edition in Blink 3.5.0 or later:

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        zkQuorum = '<host:port>', ----The Java API URL that is used to access the Enhanced Edition of an ApsaraDB for HBase database.
        userName  = 'root', --The username that is used to access the ApsaraDB for HBase database.
        password = 'root', --The password that is used to access the ApsaraDB for HBase database.
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500'
    );
    ```

-   The following sample code demonstrates how to create an ApsaraDB for HBase dimension table in Blink 3.5.0 or later that supports switchover between primary and secondary ApsaraDB for HBase databases for data writing:

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        zkQuorum = '<host:port>', ---- The URL that is used to access ApsaraDB for HBase databases in high availability (HA) mode.
        haClusterID = 'ha-xxx', ---- The instance ID of ApsaraDB for HBase databases in HA mode.
        userName  = 'root', --The username that is used to access the ApsaraDB for HBase database.
        password = 'root', --The password that is used to access the ApsaraDB for HBase database.
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500' 
    );
    ```


**Note:**

-   You can define multiple fields for the primary key. Multiple fields are separated with the value of `rowkeyDelimiter`. The default value is a colon \(`:`\).
-   When you undo the deletion operation in ApsaraDB for HBase, if a column stores multiple versions of a value, all the versions of the value are deleted.
-   The connection parameters are different between ApsaraDB for HBase Standard Edition and ApsaraDB for HBase Enhanced Edition.
    -   In ApsaraDB for HBase Standard Edition, the connection parameter is `zkQuorum`.
    -   In ApsaraDB for HBase Enhanced Edition, the connection parameter is `endPoint`.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to cloudhbase.|
|zkQuorum|The ZooKeeper address that is configured for the ApsaraDB for HBase cluster.|Yes|You can view the configuration related to hbase.zookeeper.quorum in the hbase-site.xml file. **Note:** This parameter takes effect in only ApsaraDB for HBase Standard Edition. |
|zkNodeParent|The path of the cluster configured on the ZooKeeper servers.|No|You can view the relevant configurations of hbase.zookeeper.quorum in the hbase-site.xml file. **Note:** This parameter takes effect only in ApsaraDB for HBase Standard Edition. |
|endPoint|The name of the region where your ApsaraDB for HBase instance is deployed.|Yes|You can obtain the value of this parameter from the console of your ApsaraDB for HBase instance.**Note:** This parameter takes effect only in ApsaraDB for HBase Enhanced Edition. |
|userName|The username that is used to access an ApsaraDB for HBase database.|No|This parameter takes effect only in ApsaraDB for HBase Enhanced Edition.|
|password|The password that is used to access an ApsaraDB for HBase database.|No|This parameter takes effect only in ApsaraDB for HBase Enhanced Edition.|
|tableName|The name of the ApsaraDB for HBase table.|Yes|None.|
|columnFamily|The name of the column family.|Yes|Only the same column family can be inserted.|
|maxRetryTimes|The maximum number of retries.|No|Default value: 10.**Note:** This parameter is available only in Realtime Compute for Apache Flink V3.2.3 and later. |
|bufferSize|The maximum number of data records that can be stored in the buffer before deduplication is triggered.|No|Default value: 5000.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 100.**Note:** We recommend that you set this parameter to a value that ranges from 200 to 300. A large value of batchSize may cause an out-of-memory \(OOM\) error for the task. |
|flushIntervalMs|The interval at which the buffer is cleared to reduce the latency of writing data into ApsaraDB for HBase.|No|Default value: 2000. Unit: milliseconds.|
|writePkValue|Specifies whether to write the primary key value.|No|Default value: false.|
|stringWriteMod|Specifies whether to insert data as the STRING type.|No|Default value: false.|
|rowkeyDelimiter|The delimiter of rowKey.|No|The default delimiter is a colon \(:\).|
|isDynamicTable|Specifies whether the table is a dynamic table.|No|Default value: false.|
|haClustserID|The ID of an ApsaraDB for HBase instance in HA mode.|No|This parameter is required only when you access zone-disaster recovery instances.|

## Dynamic table

Some result data of Realtime Compute for Apache Flink is used as a dynamic column based on the value of a column and written to ApsaraDB for HBase. In the following example, the turnover per hour is used as a dynamic column data in ApsaraDB for HBase.

|rowkey|cf:0|cf:1|cf:2|
|------|----|----|----|
|20170707|100|cf:1|300|

If isDynamicTable is set to true, the table is an ApsaraDB for HBase table that supports dynamic columns.

A dynamic table can contain only three columns, such as ROW\_KEY, COLUMN, and VALUE. The second column that is COLUMN in this example is a dynamic column. Other parameters in the dynamic table are the same as those in the WITH clause of ApsaraDB for HBase.

**Note:** When a dynamic table is used, all data types must be converted to the STRING type before data is written into ApsaraDB for HBase.

```
CREATE TABLE stream_test_hotline_agent (
  name varchar,
  age varchar,
  birthday varchar,
  primary key (name)
) WITH (
  type = 'cloudhbase',
  ...
  columnFamily = 'cf',
  isDynamicTable ='true'
);
```

**Note:**

-   In the preceding declaration, `birthday` is inserted into the `cf:age` column with ROW\_KEY of `name`. For example, `(wang,18,2016-12-12)` is inserted into the row for which the ROW\_KEY value is `wang` and the `cf:18` column.
-   In the DDL statement, you must declare ROW\_KEY as the primary key and declare the following fields in the descending order: ROW\_KEY, COLUMN, and VALUE. In this example, ROW\_KEY is `name`, COLUMN is `age`, and VALUE is `birthday`.

## Sample code

The following sample code demonstrates how to create an ApsaraDB for HBase result table in a Realtime Compute for Apache Flink job:

```
create table source (
  id   TINYINT,
  name BIGINT
) with (
  type = 'random'
);

create table sink (
  id    TINYINT,
  name  BIGINT,
  primary key (id)
) with (
  type = 'cloudhbase',
  zkQuorum = '<yourZkQuorum>',  
  columnFamily = '<yourColumnFamily>',
  tableName = '<yourTableName>'
);

INSERT INTO sink
SELECT id, name FROM source;
```

## FAQ

Why is the error `cloudHbase update error, No columns to insert for #10 item` reported when a job for an ApsaraDB for HBase result table is running?

The column data for a single record that is written into the ApsaraDB for HBase result table cannot all be null. The column data excludes ROW\_KEY. Before you use Realtime Compute for Apache Flink to write data into the ApsaraDB for HBase result table, filter out all null data.

