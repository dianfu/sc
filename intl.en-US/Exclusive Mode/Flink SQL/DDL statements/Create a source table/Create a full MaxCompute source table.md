---
keyword: [source table, MaxCompute]
---

# Create a full MaxCompute source table

This topic describes how to create a full MaxCompute source table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause and data type mapping involved when you create a full MaxCompute source table.

**Note:**

-   This topic applies only to Blink 2.2.7 and later.
-   Full MaxCompute source tables are typically used as bounded stream tables. This makes MaxCompute different from other data sources such as DataHub and Kafka. In Blink 3.4.4, you can specify a full MaxCompute source table as an unbounded stream table. This way, the source table can continuously listen to the generation of new partitions. If a new partition is generated, Realtime Compute for Apache Flink reads data from the new partition. This feature is deprecated in Blink 3.5.0. To use a MaxCompute source table as an unbounded stream table, see [Create an incremental MaxCompute source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Create an incremental MaxCompute source table.md).

## DDL syntax

In Realtime Compute for Apache Flink, you can use MaxCompute to store input data. The following code shows an example:

```
create table odps_source(
    id INT,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'odps',
    endPoint = 'http://service.cn.maxcompute.aliyun-inc.com/api',
    project = '<projectName>',
    tableName = '<tableName>',
    accessId = '<yourAccessKeyId>',
    accessKey = '<yourAccessKeySecret>',
    `partition` = 'ds=2018****' --If your MaxCompute source table is a non-partitioned table, you do not need to declare this parameter.
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|No|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).**Note:** This parameter is required if MaxCompute is deployed in a VPC. |
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of the MaxCompute source table.|Yes|None.|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|partition|The name of a partition.|No|-   A full MaxCompute source table that contains only one partition

For example, if only one partition key column `ds` exists, ``partition` = 'ds=20180905'` indicates that data in the `ds=20180905` partition is read.

-   A full MaxCompute source table that contains multiple partitions

For example, if two partition key columns `ds` and `hh` exist, ``partition`='ds=20180905,hh=*'` indicates that data in the `ds=20180905` partition is read.

**Note:** You must declare the values of all partitions when you filter partitions. In the preceding example, if you declare only `'partition' = 'ds=20180905'`, no partition is read. |
|subscribeNewPartition|Specifies whether to listen to new partitions that meet specific conditions.|No|Default value: false. This value indicates that the system does not listen to new partitions.**Note:**

-   If the subscribeNewPartition parameter is set to `true`, you cannot specify the value of the partition parameter. Otherwise, new partitions cannot be read.
-   This parameter is provided only in Blink 3.4.4. The parameter is decrepated in Blink 3.5.0. If you need to use this parameter, create an incremental MaxCompute source table. For more information, see [Create an incremental MaxCompute source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Create an incremental MaxCompute source table.md). |
|subscribeIntervalInSec|The interval at which new partitions are listened to.|No|Default value: 30. Unit: seconds.**Note:** If the value of this parameter is too small, pressure may be caused on the MaxCompute metadata service. This may result in failures to listen to the service. |

## Field type mapping

|MaxCompute data type|Data type of Realtime Compute for Apache Flink|
|--------------------|----------------------------------------------|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

## Sample code

The following sample code demonstrates how to create a full MaxCompute source table in a Realtime Compute for Apache Flink job.

```
CREATE TABLE odps_source (
    cid varchar,
    rt DOUBLE,
) with (
    type = 'odps', 
    endPoint = '<yourEndpointName>',
    project = '<yourProjectName>',
    tableName = '<yourTableName>',
    accessId = '<yourAccessId>',
    accessKey = '<yourAccessPassword>',
    partition = 'ds=20190712'
);

CREATE TABLE test (
    cid varchar,
    invoke_count BIGINT
) with (
  type='print'
);

INSERT INTO test 
SELECT 
    cid,
    count(*) as invoke_count
FROM odps_source GROUP BY cid;
```

## FAQ

-   Q: What do I do if the values of the endPoint and tunnelEndpont parameters in the DDL statement are incorrect?

    A: For more information about the endPoint and tunnelEndpont parameters, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md). Incorrect configuration of parameters may lead to the following issues:

    -   If the configuration of the endPoint parameter is invalid, the task publish progress stops at 91%.
    -   If the value of the tunnelEndpont parameter is invalid, the task fails.
-   Q: How does the full MaxCompute data storage read data in a full MaxCompute source table?

    A: The full MaxCompute data storage reads data from the full MaxCompute source table by using a tunnel. Therefore, the read speed and bandwidth are restricted by the bandwidth of the tunnel used by the full MaxCompute source table.

-   Q: If data of some partitions of a full MaxCompute source table has been read, can the full MaxCompute data storage read data that is newly written into these partitions after a Realtime Compute for Apache Flink job is started?

    A: No, the full MaxCompute data storage cannot read the new data from the partitions. The full MaxCompute data storage reads data from tables or partitions by using a tunnel. After a Realtime Compute for Apache Flink job is started, MaxCompute Reader exits when data reading is completed. Then, MaxCompute Reader does not read new data from the full MaxCompute source table or partitions.

-   Q: How does the full MaxCompute data storage read data that is newly written to the full MaxCompute source table or partitions after a Realtime Compute for Apache Flink job is started?

    A: Realtime Compute for Apache Flink V3.4 and later versions support the subscribeNewPartition parameter that determines whether to listen to new partitions. New data can be written into new partitions. The following code shows an example:

    ```
    CREATE TABLE blink_source (
        cid varchar,
        rt DOUBLE,
    ) with (
        type = 'odps', 
        endPoint = '<yourEndpointName>',
        project = '<yourProjectName>',
        tableName = '<yourTableName>',_table_name',
        subscribeNewPartition = 'true'
        -- You cannot specify the value of the partition parameter if you want to listen to new partitions.
        accessId = '<yourAccessKeyId>',
        accessKey = '<yourAccessKeySecret>',
    );
    
    CREATE TABLE test (
        cid varchar,
        invoke_count BIGINT
    ) with (
      type='print'
    );
    
    INSERT INTO test 
    SELECT 
        cid,
        count(*) as invoke_count
    FROM blink_source GROUP BY cid;
    ```

    **Note:** Data that is generated for new partitions in a full MaxCompute source table must be written into the new partitions of the table in Realtime Compute for Apache Flink. The data that is written into existing partitions is invalid.

-   Q: Can I use `max_pt()` or `max_pt_with_done()` in the value of the partition parameter in the WITH clause?

    A: We recommend that you do not use these parameters in the WITH clause. If you want to use these parameters, make sure that you understand the scenarios of `max_pt()` in a full MaxCompute source table in the following scenarios:

    -   Listening to new partitions is not enabled.

        After a task is started, MaxCompute Reader uses the full MaxCompute metadata service to obtain all partitions in the current full MaxCompute source table and reads `max_pt()`. After data reading is completed, MaxCompute Reader exits and does not read new data from the partition to which max\_pt belongs or listen to new partitions.

    -   Listening to new partitions is enabled.

        After a task is started, MaxCompute Reader uses the full MaxCompute metadata service to obtain all partitions in the current full MaxCompute source table and reads `max_pt()`. After data reading is completed, MaxCompute Reader does not read new data from the partition to which max\_pt\(\) belongs. However, MaxCompute Reader listens to the generation of new partitions at specific intervals. For more information, see subscribeIntervalInSec. If new partitions are generated, MaxCompute Reader reads the partitions and then reads `max_pt()` from the partitions. After data reading is completed, the system waits for the next listening event. If no new partitions are generated, the system waits for the next listening event.

-   Q: If a full MaxCompute source table is referenced as a data source, can the data that is appended to an existing partition or table be read after a job is started?

    No, the data cannot be read and the job may fail. The full MaxCompute data storage uses `ODPS DOWNLOAD SESSION` to read data from tables or partitions. When you create a `DOWNLOAD SESSION`, the MaxCompute server creates an index file, which contains the data mapping obtained when the `DOWNLOAD SESSION` is created. Subsequent data reading is performed based on the data mapping. Therefore, the data that is appended to the full MaxCompute source table or partitions after the `DOWNLOAD SESSION` is created cannot be read in normal cases. This issue occurs in the following scenarios:

    -   When the MaxCompute data storage reads data by using a tunnel, the following error is reported if new data is written into a partition: `ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`
    -   New data is written into a partition. However, the tunnel through which data is read is disabled. the data cannot be read. If a job fails or is resumed, the data may be incorrect. For example, existing data is read again but the newly added data may not be read.
-   Q: Can I suspend and resume a job for a full MaxCompute source table? Can I also change the parallelism of the full MaxCompute source table?

    A: No, you cannot suspend or resume a job for a full MaxCompute source table, or change the parallelism of the full MaxCompute source table. MaxCompute determines which data in which partitions need to be read for each concurrent job and records the consumption information for each concurrent job in the state based on the parallelism. This way, MaxCompute can continue reading data from the last read position after the job is suspended and then resumed or fails. This logic is based on the premise that the parallelism is configured. If you suspend and then resume a job for a full MaxCompute source table after you change the parallelism of the source table, the impact on the job cannot be estimated because some data may be repeatedly read but some data may not be read.

-   Q: Why are the partitions before the start offset also read when you set the start time to `2019-10-11 00:00:00` for a job?

    A: The start time is valid only for data sources of the message queue type, such as DataHub. It is invalid for full MaxCompute source tables. After you start a Realtime Compute for Apache Flink job, Realtime Compute for Apache Flink reads data in the following ways:

    -   For a partitioned table, it reads all current partitions.
    -   For a non-partitioned table, it reads all existing data.

