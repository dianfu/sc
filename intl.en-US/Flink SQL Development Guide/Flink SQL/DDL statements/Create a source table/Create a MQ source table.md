# Create a MQ source table {#concept_62523_zh .concept}

This topic describes how to create a MQ source table in Realtime Compute. It also describes the CSV format, WITH parameters, and field type mapping involved in the table creation process.

## Introduction to MQ {#section_ezv_dkz_bgb .section}

MQ is a professional message middleware that Alibaba Cloud has developed and put into commercial use. It is a core product for the enterprise-level Internet architecture \(Aliware\). Based on the high-availability distributed cluster technology, MQ provides a complete set of high-performance messaging cloud services, including publishing/subscription, message tracing, resource statistics, message scheduling \(delaying\), and monitoring and alerting. They implement all asynchronous decoupling functions in distributed computing scenarios. Realtime Compute supports creating an MQ table as the source table. The sample code is as follows.

## Examples {#section_h3x_mkz_bgb .section}

``` {#codeblock_o5l_gwx_wyq .language-sql}
create table mq_stream(
 x varchar,
 y varchar,
 z varchar
) with (
 type='mq',
 topic='yourTopicName',
 endpoint='yourEndpoint',
 pullIntervalMs='1000',
 accessId='yourAccessId',
 accessKey='yourAccessSecret',
 startMessageOffset='1000',
 consumerGroup='yourConsumerGroup',
 fieldDelimiter='|'
);
```

**Note:** MQ uses an unstructured storage format that does not force you to define a data schema. The data schema is specified at the business layer. Currently, Realtime Compute supports messages in CSV and binary formats.

## CSV format {#section_h3m_bsg_chb .section}

Assume that you have an MQ message in the following CSV format:

``` {#codeblock_iaa_irs_gxl}
1,name,male 
2,name,female
```

**Note:** An MQ message can contain zero to multiple data records separated with `\n`.

In a Realtime Compute job, the DDL statement used to declare an MQ source table is as follows:

``` {#codeblock_c96_gsa_1ys .language-sql}
create table mq_stream(
 x varchar,
 y varchar,
 z varchar
) with (
 type='mq',
 topic='yourTopicName',
 endpoint='yourEndpoint',
 pullIntervalMs='1000',
 accessId='yourAccessId',
 accessKey='yourAccessSecret',
 startMessageOffset='1000',
 consumerGroup='yourConsumerGroup',
 fieldDelimiter='|'
);
```

## Binary format {#section_xw5_5qg_chb .section}

The sample code for the binary format is as follows:

``` {#codeblock_mkp_xsc_tln .language-sql}
create table source_table (
  mess varbinary
) with (
  type = 'mq',
  endpoint = 'yourEndpoint',
  pullIntervalMs='500',
  accessId='yourAccessId',
  accessKey='yourAccessSecret',
  topic = 'yourTopicName',
  consumerGroup='yourConsumerGroup'
);

create table out_table (
  commodity varchar
)with(
  type='print'
);

INSERT INTO out_table
SELECT
  cast(mess as varchar)
FROM source_table
```

**Note:** 

-   The `cast(mess as varbinary)` statement is supported in Realtime Compute V2.0 and later. If your Realtime Compute version is earlier than V2.0, upgrade it first.
-   The VARBINARY type can be passed in only once.

## WITH parameters {#section_nnh_4kz_bgb .section}

|Name|Description|Remarks|
|----|-----------|-------|
|topic|The topic name.|None|
|endPoint|The endpoint.| -   Intranet access to Alibaba Cloud public cloud \(Alibaba Cloud classic network or VPC\): The endpoint for China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), and China \(Hong Kong\) is `onsaddr-internal.aliyun.com:8080`.
-   Internet access to Alibaba Cloud public cloud: The endpoint is `http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet`.

 |
|accessId|The AccessKey ID.|None|
|accessKey|The AccessKey Secret.|None|
|consumerGroup|The consumer group that subscribes to the topic.|None|
|pullIntervalMs|The pull interval.|Unit: millisecond.|
|startTime|The start time of message consumption.|Optional.|
|startMessageOffset|The start offset of messages.|Optional. If this parameter is set, the loading preferentially starts from the checkpoint determined by the offset.|
|tag|The subscription tag.|Optional.|
|lineDelimiter|The line delimiter used to parse message blocks.|Optional. Default value: `\n`.|
|fieldDelimiter|The field delimiter.|Optional. Default value: `\u0001`. This value indicates that `\u0001` is used as the delimiter in read-only mode and `^A` is used as the delimiter in edit mode. \\u0001 is invisible in read-only mode.|
|encoding|The encoding format.|Optional. Default value: `UTF-8`.|
|lengthCheck|The policy for checking the number of fields in a single line.|Optional. Default value: NONE. Valid values: NONE, SKIP, EXCEPTION, and PAD. -   SKIP: skips a data record when the number of fields in the record does not match the specified number.
-   EXCEPTION: throws an exception when the number of fields in the record does not match the specified number.
-   PAD: pads fields in sequence. Pad with null when a field does not exist.

 |
|columnErrorDebug|Indicates whether to enable debugging.|Optional. Default value: false. If this parameter is set to true, logs about parsing exceptions are displayed.|

## Field type mapping {#section_hkx_4kz_bgb .section}

|MQ field type|Recommended Realtime Compute field type|
|-------------|---------------------------------------|
|STRING|VARCHAR|

