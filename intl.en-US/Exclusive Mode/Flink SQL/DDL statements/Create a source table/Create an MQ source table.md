---
keyword: [Message Queue, source table, MQ]
---

# Create an MQ source table

This topic describes how to create a Message Queue \(MQ\) source table in Realtime Compute for Apache Flink. It also describes the comma-separated values \(CSV\) file format, parameters in the WITH clause, and data type mapping used when you create such a source table.

**Note:** To use MQ with separate namespaces, use the Blink 3.X version.

## Introduction to MQ

MQ is a professional messaging middleware that is developed by Alibaba Cloud. It is a core product of the enterprise-level Internet architecture. In Realtime Compute for Apache Flink, you can use MQ tables as source tables for the processing of streaming data.

## Example

```
create table mq_stream(
  x varchar,
  y varchar,
  z varchar
) with (
  type='mq',
  topic='<yourTopicName>',
  endpoint='<yourEndpoint>',
  pullIntervalMs='1000',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  startMessageOffset='1000',
  consumerGroup='<yourConsumerGroup>',
  fieldDelimiter='|'
);
```

**Note:** MQ stores unstructured data. You do not need to define schemas for MQ source tables. Realtime Compute for Apache Flink supports messages in the CSV and binary formats.

## CSV format

Assume that you have an MQ message in the following CSV format:

```
1,name,male 
2,name,female
```

**Note:** An MQ message can contain any number of data records. Multiple data records are separated by `\n`.

You can use the following DDL statement to declare an MQ source table in a Realtime Compute for Apache Flink job:

```
create table mq_stream(
  id varchar,
  name varchar,
  gender varchar
) with (
  type='mq',
  topic='<yourTopicName>',
  endpoint='<ourEndpoint>',
  pullIntervalMs='1000',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  startMessageOffset='1000',
  consumerGroup='<yourConsumerGroup>',
  fieldDelimiter='|'
);
```

## Binary format

For binary-formatted messages, you can use the following sample code to create an MQ source table:

```
create table source_table (
  mess varbinary
) with (
  type = 'mq',
  endpoint = '<yourEndpoint>',
  pullIntervalMs='500',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  topic = '<yourTopicName>',
  consumerGroup='<yourConsumerGroup>'
);

create table out_table (
  commodity varchar
) with (
  type='print'
);

INSERT INTO out_table
SELECT 
  cast(mess as varchar)
FROM source_table;
```

**Note:**

-   The `cast (mess as varbinary)` method is supported only in Realtime Compute for Apache Flink V2.0.0 and later. If your Realtime Compute for Apache Flink version is earlier than V2.0.0, upgrade it first.
-   Data of the VARBINARY type can be passed in only once.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to mq.|
|topic|The name of a topic.|Yes|None.|
|endPoint|The endpoint of MQ.|Yes|Alibaba Cloud MQ provides two types of MQ services: internal MQ service and Internet MQ service. Select the correct endpoint based on the MQ type that you have purchased. -   Endpoints for internal MQ service \(Alibaba Cloud classic network or VPC\):
    -   China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), and China \(Hong Kong\): `onsaddr-internal.aliyun.com:8080`.
    -   Singapore \(Singapore\): `ap-southeastaddr-internal.aliyun.com:8080`.
    -   UAE \(Dubai\): `ons-me-east-1-internal.aliyuncs.com:8080`.
    -   India \(Mumbai\): `ons-ap-south-1-internal.aliyuncs.com:8080`.
    -   Malaysia \(Kuala Lumpur\): `ons-ap-southeast-3-internal.aliyun.com:8080`.
-   Endpoint for Internet MQ service: `onsaddr-internet.aliyun.com:80`.

**Note:**

-   Internal MQ service does not support cross-origin access. For example, if your Realtime Compute for Apache Flink service is located in the China \(Hangzhou\) region but your MQ service is located in the China \(Shanghai\) region, Realtime Compute for Apache Flink cannot access this MQ service.
-   Due to changes in the network security policies of Alibaba Cloud, connection issues may occur when Realtime Compute for Apache Flink connects to the Internet MQ service. We recommend that you use the internal MQ service. If an exception occurs when you use the Internet MQ service,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23). |
|accessId|The AccessKey ID that is used to log on to the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to log on to the database.|Yes|None.|
|consumerGroup|The name of a consumer group.|Yes|None.|
|pullIntervalMs|The interval at which messages are pulled.|No|Unit: milliseconds.|
|startTime|The start time of message consumption.|No|None.|
|startMessageOffset|The offset from the start time.|No|This parameter is optional. If you set this parameter, messages are consumed from the time point specified by this parameter.|
|tag|A subscription tag.|No|None.|
|lineDelimiter|A row delimiter that is used to parse message blocks.|No|Default value: `\n`.|
|fieldDelimiter|A field delimiter.|No|Default value: `\u0001`. `\u0001` is the field delimiter in read-only mode, and `^A` is the field delimiter in edit mode. `\u0001` is invisible in read-only mode.|
|encoding|The encoding format.|No|Default value: `utf-8`.|
|lengthCheck|The policy for checking the number of fields parsed from a row of data.|No|Default value: NONE. -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is extracted from left to right based on the order of defined fields.
-   If the number of fields parsed from a row of data is less than the number of defined fields, this row of data is skipped.

Other valid values are SKIP, EXCEPTION, and PAD. -   SKIP: If the number of fields parsed from a row of data does not match the number of defined fields, this row of data is skipped.
-   EXCEPTION: If the number of fields parsed from a row of data does not match the number of defined fields, an exception is reported.
-   PAD: Data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the number of defined fields, the values of the missing fields are padded with null. |
|columnErrorDebug|Specifies whether debugging is enabled.|No|Default value: FALSE. If you set this parameter to TRUE, a log entry is printed when a parsing exception occurs.|
|instanceID|The ID of an MQ instance.|No|If the MQ instance does not have a separate namespace, you do not need to set this parameter. If the MQ instance has a separate namespace, this parameter is required.|

## Field type mapping

|MQ data type|Data type of Realtime Compute for Apache Flink|
|------------|----------------------------------------------|
|STRING|VARCHAR|

