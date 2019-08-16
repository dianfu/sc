# Create a MQ result table {#concept_62528_zh .concept}

This topic describes how to create an MQ result table in Realtime Compute.

## Introduction to MQ {#section_jnb_3qf_cgb .section}

MQ is a professional message middleware that Alibaba Cloud has developed and put into commercial use. It is a core product for the enterprise-level Internet architecture \(Aliware\). Based on the high-availability distributed cluster technology, MQ provides a complete set of high-performance messaging cloud services, including publishing/subscription, message tracing, resource statistics, message scheduling \(delaying\), and monitoring and alerting. Realtime Compute supports creating an MQ table as the result table. The sample code is as follows:

``` {#codeblock_jvs_1vy_u38 .language-sql}
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content VARCHAR
) WITH (
type='mq',
 endPoint=‘<yourEndpoint>’,
 accessId=‘<yourAccessId>’,
 accessKey=‘<yourAccessSecret>’,
 project=‘<yourProjectName>’,
 producerGroup='<yourGroupName>',
 tag='<yourTagName>',
 encoding='utf-8',
 fieldDelimiter=',',
 retryTimes='5',
 sleepTimeMs='500'
);
```

## CSV format {#section_tvd_lqg_chb .section}

``` {#codeblock_4k7_3nf_ono .language-sql}





CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content varchar
) WITH (
type='mq',
 endPoint=‘<yourEndpoint>’,
 accessId=‘<yourAccessId>’,
 accessKey=‘<yourAccessSecret>’,
 project=‘<yourProjectName>’,
 producerGroup='<yourGroupName>',
 tag='<yourTagName>',
 encoding='utf-8',
 fieldDelimiter=',',
 retryTimes='5',
 sleepTimeMs='500'
);
```

## Binary format {#section_xw5_5qg_chb .section}

The sample code for the binary format is as follows:

``` {#codeblock_2dy_9mk_2kt .language-sql}
create table source_table (
  commodity VARCHAR
)with(
  type='random'
);

create table result_table (
  mess varbinary
) with (
  type = 'mq',
  topic = '<yourTopicName>',
  endPoint=‘<yourEndpoint>’,
  accessId=‘<yourAccessId>’,
  accessKey=‘<yourAccessSecret>’,
  producerGroup='<yourGroupName>'
);

INSERT INTO result_table
SELECT 
cast(substring(commodity,0,5) as varbinary) as mess   
FROM source_table
```

**Note:** 

-   The `cast(varchar as varbinary)` statement is supported in Realtime Compute V2.0 and later. If your Realtime Compute version is earlier than V2.0, upgrade it first.
-   The VARBINARY type can be passed in only once.

## WITH parameters {#section_glb_mqf_cgb .section}

|Name|Description|Remarks|
|----|-----------|-------|
|topic|The name of the MQ queue.|None|
|endpoint|The endpoint.| -   Intranet access to Alibaba Cloud public cloud \(Alibaba Cloud classic network or VPC\): The endpoint for China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), and China \(Hong Kong\) is `onsaddr-internal.aliyun.com:8080`.
-   Internet access to Alibaba Cloud public cloud: The endpoint is `http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet`.

 |
|accessID|The AccessKey ID.|None|
|accessKey|The AccessKey Secret.|None|
|producerGroup|The name of the producer group.|None|
|tag|The message tag.|Optional. This parameter is left empty by default.|
|fieldDelimiter|The field delimiter.|Optional. Default value: `\u0001`. This value indicates that `\u0001` is used as the delimiter in read-only mode and `^A` is used as the delimiter in edit mode. `\u0001` is invisible in read-only mode.|
|encoding|The encoding format.|Optional. Default value: `UTF-8`.|
|retryTimes|The maximum number of write retries allowed.|Optional. Default value: 10.|
|sleepTimeMs|The retry interval, in milliseconds.|Optional. Default value: 1000.|

