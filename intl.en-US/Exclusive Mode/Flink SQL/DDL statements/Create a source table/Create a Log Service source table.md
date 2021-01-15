# Create a Log Service source table

This topic describes how to create a Log Service source table in Realtime Compute for Apache Flink. It also describes the attribute fields, parameters in the WITH clause, and data type mapping involved when you create a Log Service source table.

**Note:** This topic applies only to Blink 1.4.5 and later.

## Introduction to Log Service

Log Service is an end-to-end service for log data. The data format of Log Service is similar to JSON. The following code shows an example:

```
{
    "a": 1000,
    "b": 1234,
    "c": "li"
}
```

Log Service is used to store streaming data. Therefore, Realtime Compute for Apache Flink can use the streaming data that is stored in Log Service as input data.

## Syntax

The following sample code demonstrates how to create a Log Service source table in a data definition language \(DDL\) statement. In the code, `sls` indicates Log Service.

```
create table sls_stream(
  a INT,
  b INT,
  c VARCHAR
) with (
  type ='sls',  
  endPoint ='http://cn-hangzhou-share.log.aliyuncs.com',
  accessId ='<yourAccessId>',
  accessKey ='<yourAccessKey>',
  startTime = '2017-07-05 00:00:00',
  project ='<yourProjectName>',
  logStore ='<yourLogStoreName>',
  consumerGroup ='<yourConsumerGroupName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to sls.|
|endPoint|The endpoint of Log Service.|Yes|[Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md)|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|project|The name of the Log Service project from which data is read.|Yes|None.|
|logStore|The name of the Logstore in the Log Service project.|Yes|None.|
|startTime|The time when a log starts to be consumed.|No|None.|
|consumerGroup|The name of a consumer group.|No|You can set this parameter as required. The format of the name is not fixed.|
|heartBeatIntervalMills|The heartbeat interval of the consumer client.|No|Default value: 10000. Unit: milliseconds.|
|maxRetryTimes|The maximum number of retries for reading data.|No|Default value: 5.|
|batchGetSize|The number of log items that are read from a log group at a time.|No|Default value: 100.|
|columnErrorDebug|Specifies whether to enable debugging.|No|Default value: false. This indicates that debugging is disabled. If you enable debugging, logs that contain parsing exceptions are printed.|
|startupMode|Specifies whether to enable the consumption mode.|No|Valid values: -   TIMESTAMP: Realtime Compute for Apache Flink starts to consume data from a specified time point in each shard. This value is the default value.
-   Earliest: Realtime Compute for Apache Flink starts to consume data from the earliest offset in each shard.
-   Latest: Realtime Compute for Apache Flink starts to consume data from the latest offset in each shard.
-   Group\_Offsets: Realtime Compute for Apache Flink preferentially consumes data from the checkpoint that is stored on the server in each shard. You must specify the consumerGroup parameter. The consumption mode varies based on the following scenarios:
    -   If recovery from the Flink state is successful, Realtime Compute for Apache Flink starts to consume data from the checkpoint of the Flink state.
    -   If recovery from the Flink state fails, the consumption mode varies based on the following scenarios:
        -   If a checkpoint is available for the specified consumer group, Realtime Compute for Apache Flink attempts to consume data from this checkpoint.
        -   If no checkpoint is available for the specified consumer group, the consumption mode varies based on the following scenarios:
            -   If you specify startTime, Realtime Compute for Apache Flink starts to consume data at the specified start time.
            -   If you do not specify startTime, Realtime Compute for Apache Flink starts to consume data from the earliest offset in each shard.

**Note:**

-   Only Blink 3.6.5 and later versions support this parameter.
-   The preceding configurations take effect only when no checkpoint exists in the state data. |

**Note:**

-   In Realtime Compute for Apache Flink V1.6.0 and earlier versions, the read performance may be affected if the number of shards in a consumer group is specified. This issue is being rectified.
-   Log Service does not support the MAP data type.
-   Log Service sets the fields that do not exist to null.
-   We recommend that you define the fields in the same order as the fields in the source table. Unordered fields are also supported.
-   If input data is in the JSON format, define a separator and use the built-in function [JSON\_VALUE](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/String functions/JSON_VALUE.md) to analyze the data. Otherwise, the following parsing error is returned:

    ```
    2017-12-25 15:24:43,467 WARN [Topology-0 (1/1)] com.alibaba.blink.streaming.connectors.common.source.parse.DefaultSourceCollector - Field missing error, table column number: 3, data column number: 3, data filed number: 1, data: [{"lg_order_code":"LP00000005","activity_code":"TEST_CODE1","occur_time":"2017-12-10 00:00:01"}]                
    ```

-   The value of the batchGetSize parameter must be less than or equal to 1000. Otherwise, an error is returned.
-   The batchGetSize parameter specifies the number of log items that are read at a time in a log group. If the value of the batchGetSize parameter and the size of a single log item that is specified in LogItem are large, frequent garbage collections \(GCs\) may occur. In this case, you must reduce the value of the batchGetSize parameter.

## Field type mapping

The following table describes the mapping between the field data types of Log Service and Realtime Compute for Apache Flink. We recommend that you declare the mapping in DDL statements.

|Data type of Log Service|Data type of Realtime Compute for Apache Flink|
|------------------------|----------------------------------------------|
|STRING|VARCHAR|

## Attribute fields

By default, Flink SQL supports retrieving three types of Log Service attribute fields. Custom fields are also supported as the input. For more information about how to use the attribute fields, see [Obtain attribute fields of a source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md).

|Field|Description|
|-----|-----------|
|`__source__`|The message source.|
|`__topic__`|The message topic.|
|`__timestamp__`|The time when a log was generated.|

## Sample code

```
create table sls_input(
  a int, 
  b int,
  c varchar
) with (
  type ='sls',
  endPoint ='http://cn-hangzhou-share.log.aliyuncs.com',
  accessId ='<yourAccessI>',
  accessKey ='<yourAccessKey>',
  startTime = '2017-07-05 00:00:00',
  project ='ali-cloud-streamtest',
  logStore ='stream-test',
  consumerGroup ='consumerGroupTest1'
);

create table print_output(
 a int,
 b int,
 c varchar 
) with (
  type='print'
);

INSERT INTO print_output
SELECT 
  a, b, c
from sls_input;
```

## FAQ

-   Q: Why does the overall latency of a job increase, or why is no output generated for the job that has window aggregation?

    A: This issue occurs if no new data is written into a partition. To solve this issue, change the parallelism to be the same as the number of read and write partitions.

-   Q: How do I set the parallelism?

    A: We recommend that you set the parallelism to the number of partitions. Otherwise, if the speeds at which data is read from two partitions vary significantly, data may be filtered out or data latency may occur when you set the start offset for a job to a time prior to the present time.

-   Q: How do I troubleshoot the issue that the latency of a Flink job increases?

    A: The Log Service source table may be sharded. Shard indexes may not be continuous after sharding, which increases the latency of a Flink job. If you find that the latency of a Flink job increases, check whether the Log Service source table is sharded.

-   Q: How do I obtain attribute fields?

    A: For more information about how to obtain attribute fields, see [Obtain attribute fields of a source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md).

    **Note:** Attribute fields cannot be extracted during local debugging. We recommend that you perform online debugging and view the attribute fields in logs. For more information, see [Online debugging](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Job debugging/Online debugging.md).


## References

-   For more information about Log Service, see [What is Log Service?](/intl.en-US/Product Introduction/What is Log Service?.md).
-   For more information about how to consume Log Service data in Realtime Compute for Apache Flink, see [Use Realtime Compute to consume log data](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Use Realtime Compute to consume log data.md).

