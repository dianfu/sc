---
keyword: [result table, MaxCompute]
---

# Create a MaxCompute result table

This topic describes how to create a MaxCompute result table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause, data type mapping, and FAQ involved when you create a MaxCompute result table.

**Note:**

-   Only Blink 1.5.1 and later versions support MaxCompute result tables.
-   A clustered table of MaxCompute cannot be used as a result table.

## Implementation

The MaxCompute sink works in two phases:

1.  Writes data. The MaxCompute sink calls an API operation in the MaxCompute SDK to write data into the buffer. Then, the sink uploads data to the temporary files of MaxCompute at a specified interval or when the data size in the buffer exceeds 64 MB.
2.  Commits sessions. When a task runs a checkpoint, the MaxCompute sink calls the COMMIT method of Tunnel to commit sessions, moves temporary files to the data directory of the MaxCompute table, and modifies the metadata.

    **Note:** The COMMIT method does not provide atomicity. Therefore, the MaxCompute sink supports at-least-once delivery instead of exactly-once delivery.


## Syntax

In Realtime Compute for Apache Flink, you can use MaxCompute to store output data. The following code shows an example:

**Note:** The names, sequence, and types of the fields defined in the data definition language \(DDL\) statement must be the same as those in the MaxCompute physical table. Otherwise, the queried data in the MaxCompute physical table may be `/n`.

```
create table odps_output(
    id INT,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'odps',
    endPoint = '<YourEndPoint>',
    project = '<YourProjectName>',
    tableName = '<YourtableName>',
    accessId = '<yourAccessKeyId>',
    accessKey = '<yourAccessKeySecret>',
    `partition` = 'ds=2018****'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to `odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md). **Note:** This parameter is required if MaxCompute is deployed in a VPC. |
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of a table.|Yes|None.|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|partition|The name of a partition.|No|This parameter is required if a partitioned table exists. -   Fixed partition

For example, ``partition` = 'ds=20180905'` indicates that data is written into partition `ds= 20180905`.

-   Dynamic partition \(available in Blink 3.2.1 and later\)

If the partition values are not displayed in plaintext mode, data is written into different partitions based on the values of the partition key columns specified in the data. For example, ``partition`='ds'` indicates that data is written into partitions based on the value of the `ds` field.

If you want to create multi-level dynamic partitions, make sure that the sequence of the partition fields in the WITH clause and DDL statement of the MaxCompute result table are consistent with the field sequence of the MaxCompute physical table. Multiple partition fields are separated by commas \(,\).

**Note:**

    -   In the CREATE TABLE statement, you must explicitly specify the partition key column that you use to create dynamic partitions.
    -   If the partition field for dynamic partitions is left empty and the value of the ds field is `null` or `''`, the output varies based on the Blink version:
        -   For Blink 3.2.1 and earlier, a NullPointerException \(NPE\) error is returned.
        -   For Blink 3.2.2 and later, a partition with ds=null is created. |
|flushIntervalMs|The flush interval for the buffer of a writer in MaxCompute Tunnel. Unit: milliseconds. The MaxCompute sink first inserts data into its buffer. Then, the MaxCompute sink writes the data in the buffer to the destination MaxCompute table at an interval that is specified by the flushIntervalMs parameter. The sink also writes the data to the destination table when the size of the buffer data exceeds the specified threshold.

|No|Default value: 30000. Unit: milliseconds.**Note:** This parameter is available in Blink 3.6.0 and later. |
|partitionBy|The columns based on which hash shuffling is implemented. Before data is written into a sink node, the system implements hash shuffling based on the value of this parameter. This way, data flows to the destination sink node.|No|The system implements hash shuffling based on multiple columns. The column names are separated by commas \(,\). This parameter is empty by default.**Note:** This parameter is available in Blink 3.6.0 and later. |
|isOverwrite|Specifies whether to clear the result table before data is written to a sink node.|No|-   For versions earlier than Blink 3.2, the default value is true.
-   For Blink 3.2 and later, the default value is false. When you declare a MaxCompute result table for a streaming job, you must set the isOverwrite parameter to false. Otherwise, an error is returned during compilation.

**Note:** For Blink 3.2 and later, you can change the value of the isOverwrite parameter to true. For Blink versions earlier than 3.2, if you want to change the value of the isOverwrite parameter, you must upgrade the Blink version first. |
|dynamicPartitionLimit|The number of partitions in the underlying framework.|No|The default value is 100. A map in the memory maintains the mappings between the existing partitions to which data is written and the Tunnel service or the writer. If the map size exceeds the value of the dynamicPartitionLimit parameter, the system reports the following error: `Too many dynamic partitions: 100, which exceeds the size limit: 100`.|

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
|STRING|VARCHAR|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|

## Sample code

The following sample code demonstrates how to create a MaxCompute result table in a Realtime Compute for Apache Flink job.

-   Write data into a fixed partition

    ```
    CREATE TABLE source (
       id INT,
       len INT,
       content VARCHAR
    ) with (
       type = 'random'
    );
    
    create table odps_sink (
       id INT,
       len INT,
       content VARCHAR
    ) with (
       type = 'odps',
       endPoint = '<yourEndpoint>', 
       project = '<yourProjectName>',
       tableName = '<yourTableName>',
       accessId = '<yourAccessId>',
       accessKey = '<yourAccessPassword>',
       `partition` = 'ds=20180418'
    );
    
    INSERT INTO odps_sink 
    SELECT 
       id, len, content 
    FROM source;
    ```

-   Write data into a dynamic partition

    ```
    CREATE TABLE source (
       id INT,
       len INT,
       content VARCHAR,
       c TIMESTAMP 
    ) with (
       type = 'random'
    );
    
    create table odps_sink (
       id INT,
       len INT,
       content VARCHAR,
       ds VARCHAR --The partition key column that you use to create dynamic partitions must be explicitly specified in the CREATE TABLE statement.
    ) with (
       type = 'odps',
       endPoint = '<yourEndpoint>', 
       project = '<yourProjectName>',
       tableName = '<yourTableName>',
       accessId = '<yourAccessId>',
       accessKey = '<yourAccessPassword>',
       `partition`='ds' --The partition value is not provided. This means that data is written into different partitions based on the value of the ds field.
    );
    
    INSERT INTO odps_sink 
    SELECT 
       id, 
       len, 
       content,
       DATE_FORMAT(c, 'yyMMdd') as ds
    FROM source;
    ```


## FAQ

-   What do the endPoint and tunnelEndpoint parameters specify in the Alibaba Cloud public cloud? What happens if the parameter configurations are invalid?

    For more information about the endPoint and tunnelEndpoint parameters, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md). If the configuration of the two parameters is invalid in a VPC, a task exception may occur.

    -   If the configuration of the endPoint parameter is invalid, the task stops at the progress of 91%.
    -   If the configuration of the tunnelEndpoint parameter is invalid, the task fails.
-   Q: Does Realtime Compute for Apache Flink clear a MaxCompute result table before it writes data into the result table in stream mode when isOverwrite is set to true?

    A: Blink versions earlier than 3.2 support this feature. Blink 3.2 and later versions do not support this feature.

    If the isOverwrite parameter is set to true, Realtime Compute for Apache Flink clears a MaxCompute result table before it writes data into the result table. Realtime Compute for Apache Flink clears data from the existing result table or the result partition each time a job is started or resumed, or before Realtime Compute for Apache Flink writes data into the result table.

    -   For Blink versions earlier than 3.2, the default value of isOverwrite is true and cannot be changed. Data may be lost after a streaming job is suspended or resumed.
    -   For Blink 3.2 and later, the default value of isOverwrite is false. If you declare a MaxCompute result table for a streaming job, you must set the isOverwrite parameter to false. Otherwise, an error is returned during compilation. MaxCompute result tables in stream mode support the at-least-once data security mechanism. If a job fails, duplicate data may be generated.

