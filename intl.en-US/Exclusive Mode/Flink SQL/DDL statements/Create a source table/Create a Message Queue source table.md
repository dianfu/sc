---
keyword: [Message Queue, source table, MQ]
---

# Create a Message Queue source table

This topic describes how to create a Message Queue source table in Realtime Compute for Apache Flink. It also describes the comma-separated values \(CSV\) file format, parameters in the WITH clause, and data type mapping used when you create a Message Queue source table.

**Note:** If you need to use Message Queue with separate namespaces, use Blink 3.X.

## Introduction to Message Queue

Message Queue is a professional messaging middleware that is developed by Alibaba Cloud. It is a core product of the enterprise-level Internet architecture. In Realtime Compute for Apache Flink, you can use Message Queue tables as source tables for the processing of streaming data.

## Sample code

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

**Note:** Message Queue stores unstructured data. You do not need to define schemas for Message Queue source tables. Realtime Compute for Apache Flink supports messages in the CSV and binary formats.

## CSV format

Assume that you have a Message Queue message in the following CSV format:

```
1,name,male 
2,name,female
```

**Note:** A Message Queue message can contain any number of data records. Multiple data records are separated by `\n`.

You can use the following DDL statement to declare a Message Queue source table in a Realtime Compute for Apache Flink job:

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

For binary-formatted messages, you can use the following sample code to create a Message Queue source table:

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

-   The `cast (mess as varbinary)` method is supported only in Realtime Compute for Apache Flink V2.0 and later. If your Realtime Compute for Apache Flink version is earlier than V2.0, upgrade it first.
-   Data of the VARBINARY type can be passed in only once.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to mq.|
|topic|The name of a topic.|Yes|None.|
|endPoint|The endpoint of Message Queue.|Yes|Alibaba Cloud Message Queue provides two types of Message Queue services: internal Message Queue service and Internet Message Queue service. Select the valid endpoint based on the type of Message Queue that you purchase.-   Endpoints for internal Message Queue service \(Alibaba Cloud classic network or VPC\):
    -   China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), and China \(Hong Kong\): `onsaddr-internal.aliyun.com:8080`.
    -   Singapore \(Singapore\): `ap-southeastaddr-internal.aliyun.com:8080`.
    -   UAE \(Dubai\): `ons-me-east-1-internal.aliyuncs.com:8080`.
    -   India \(Mumbai\): `ons-ap-south-1-internal.aliyuncs.com:8080`.
    -   Malaysia \(Kuala Lumpur\): `ons-ap-southeast-3-internal.aliyun.com:8080`.
-   Endpoint for Internet Message Queue service: `onsaddr-internet.aliyun.com:80`.

**Note:**

-   Internal Message Queue service does not support cross-origin access. For example, if your Realtime Compute for Apache Flink service is located in the China \(Hangzhou\) region but your Message Queue service is located in the China \(Shanghai\) region, Realtime Compute for Apache Flink cannot access this Message Queue service.
-   Due to changes in the network security policies of Alibaba Cloud, connection issues may occur when Realtime Compute for Apache Flink connects to the Internet Message Queue service. We recommend that you use the internal Message Queue service. If an exception occurs when you use the Internet Message Queue service,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23). |
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
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
|columnErrorDebug|Specifies whether to enable debugging.|No|Default value: FALSE. If you set this parameter to TRUE, a log entry is printed when a parsing exception occurs.|
|instanceID|The ID of a Message Queue instance.|No|If the Message Queue instance does not have separate namespaces, you do not need to set this parameter. If the Message Queue instance has separate namespaces, this parameter is required.|

## Mapping between field data types

|Message Queue data type|Data type of Realtime Compute for Apache Flink|
|-----------------------|----------------------------------------------|
|STRING|VARCHAR|

