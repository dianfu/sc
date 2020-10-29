---
keyword: [Log Service, result table]
---

# Create a Log Service result table

This topic describes how to create a Log Service result table.

**Note:**

-   This topic applies only to Blink 1.4.5 and later.
-   Log Service result tables support only fields of the VARCHAR type.

## Introduction to Log Service

Log Service is an end-to-end service for log data. It helps you quickly collect, consume, ship, query, and analyze data to improve O&M and operations efficiency and build up the capabilities to process a large number of logs. Log Service stores streaming data. Therefore, Realtime Compute for Apache Flink can use Log Service tables as result tables for the processing of streaming data.

## DDL syntax

In Realtime Compute for Apache Flink, you can use Log Service to store output data. The following code shows an example:

```
create table sls_stream(
 `name` VARCHAR,
 age BIGINT,
 birthday BIGINT
)with(
 type='sls',
 endPoint='http://cn-hangzhou-corp.sls.aliyuncs.com',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
 project='<yourProjectName>',
 logstore='<yourLogstoreName>'
);
```

**Note:** We recommend that you use the storage registration feature. For more information, see [Register Log Service resources](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register Log Service resources.md).

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|endPoint|The endpoint of Log Service.|Yes|[Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md)|
|project|The name of a Log Service project.|Yes|None.|
|logstore|The name of the table in the database.|Yes|None.|
|accessId|The AccessKey ID that is used to log on to the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to log on to the database.|Yes|None.|
|topic|An attribute field.|No|This parameter is empty by default. You can select a field as the `topic` attribute field.|
|timestampColumn|An attribute field.|No|This parameter is empty by default. You can select a field as the `timestamp` attribute field. The selected field must be of the INT type. If you do not specify an attribute field, the current time is used by default. **Note:** This parameter is available in Realtime Compute for Apache Flink V2.2.2 and later. |
|source|An attribute field. The source of a log entry. For example, the value of this parameter can be the IP address of the machine where the log entry is generated.|No|This parameter is empty by default. You can select a field as the `source` attribute field.|
|partitionColumn|The partition key column.|No|This parameter is required if the `mode` parameter is set to `partition`.|
|flushIntervalMs|The interval at which data writing is triggered.|No|Default value: 2000. Unit: milliseconds.|
|reserveMilliSecond|Specifies whether to reserve the millisecond component in a value of the TIMESTAMP type.|No|Default value: false. This value indicates that the millisecond component is not reserved. **Note:** This parameter is available in Realtime Compute for Apache Flink V2.2.6 and later. |

## Field type mapping

The following table describes the mapping between the data types of Log Service and Realtime Compute for Apache Flink. We recommend that you declare the mapping in a data definition language \(DDL\) statement.

|Data type of Log Service|Data type of Realtime Compute for Apache Flink|
|------------------------|----------------------------------------------|
|STRING|VARCHAR|

## Sample code

The following sample code demonstrates how to create a Log Service result table in a Realtime Compute for Apache Flink job.

```
CREATE TABLE random_input (
  a VARCHAR, 
  b VARCHAR) with (
    type = 'random'
);

create table sls_output(
 a varchar,
 b varchar
)with(
 type='sls',
 endPoint='http://cn-hangzhou-corp.sls.aliyuncs.com',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
 project='ali-cloud-streamtest',
 logStore='stream-test2'
);

INSERT INTO sls_output
SELECT a, b
FROM random_input;
```

## FAQ

Q: How do I configure the topic field for a Log Service result table?

A: You can configure the topic field as a field in the result table, for example, set `topic='age'` in the sample code. After the configuration is completed, the value of the `age` field is written into Log Service but Log Service does not write the `age` field into the downstream storage systems.

## References

-   For more information about Log Service, see [What is Log Service?](/intl.en-US/Product Introduction/What is Log Service?.md).
-   For more information about how to consume Log Service data in Realtime Compute for Apache Flink, see [Use Realtime Compute to consume log data](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Use Realtime Compute to consume log data.md).

