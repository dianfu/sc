---
keyword: [Message Queue, result table, MQ]
---

# Create a Message Queue result table

This topic describes how to create a Message Queue result table in Realtime Compute for Apache Flink. This topic also describes the parameters in the WITH clause. The parameters are used when you create the result table.

**Note:**

-   This topic applies to only Blink V1.4.5 and later.
-   If you need to use Message Queue that has separate namespaces, use Blink V3.x.

## Introduction to Message Queue

Message Queue is a professional message middleware that is developed by Alibaba Cloud for commercial use. It is a core product for the enterprise-level Internet architecture. Based on the high-availability distributed cluster technology, Message Queue provides comprehensive cloud messaging services, including message publishing and subscription, message tracing, resource statistics, message scheduling or delaying, monitoring, and alerts.

## CSV format

Realtime Compute for Apache Flink writes streaming data to Message Queue result tables for output. In the following sample code, the data definition language \(DDL\) statement creates a Message Queue result table to store streaming data in the CSV format:

```
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content VARCHAR
) WITH (
type='mq',
endpoint='<yourEndpoint>',
accessID='<yourAccessId>',
accessKey='<yourAccessSecret>',
topic='<yourTopicName>',
producerGroup='<yourGroupName>',
tag='<yourTagName>',
encoding='utf-8',
fieldDelimiter=',',
retryTimes='5',
sleepTimeMs='500'
);
```

## Binary format

Realtime Compute for Apache Flink writes streaming data to Message Queue result tables for output. In the following sample code, the DDL statement creates a Message Queue result table to store streaming data in the binary format:

```
CREATE TABLE source_table (
  commodity VARCHAR
)WIHT(
  type='random'
);

CREATE TABLE result_table (
  mess VARBINARY
) WITH (
  type = 'mq',
  endpoint='<yourEndpoint>',
  accessID='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  topic='<yourTopicName>',
  producerGroup='<yourGroupName>'
);

INSERT INTO result_table
SELECT 
CAST(SUBSTRING(commodity,0,5) AS VARBINARY) AS mess   
FROM source_table;
```

**Note:** Blink V2.0 or later supports `cast(varchar as varbinary)`. If the Blink version is earlier than V2.0, upgrade the Blink version first. For more information, see [Manage Blink versions of a Realtime Compute for Apache Flink cluster deployed in exclusive mode](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Manage Blink versions of a Realtime Compute for Apache Flink cluster deployed in exclusive
         mode.md).

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|topic|The name of the Message Queue topic to which data is written.|None|
|endpoint|The endpoint that is used to access Message Queue.|Alibaba Cloud Message Queue provides two types of Message Queue services: internal Message Queue service and Internet Message Queue service. Select the valid endpoint based on the type of Message Queue that you purchase. -   Endpoints for the internal Message Queue service that is deployed in the Alibaba Cloud classic network or VPC:
    -   China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Zhangjiakou-Beijing Winter Olympics\), China \(Shenzhen\), and China \(Hong Kong\): `onsaddr-internal.aliyun.com:8080`.
    -   Singapore \(Singapore\): `ap-southeastaddr-internal.aliyun.com:8080`.
    -   UAE \(Dubai\): `ons-me-east-1-internal.aliyuncs.com:8080`.
    -   India \(Mumbai\): `ons-ap-south-1-internal.aliyuncs.com:8080`.
    -   Malaysia \(Kuala Lumpur\): `ons-ap-southeast-3-internal.aliyun.com:8080`.
-   Endpoint for the Internet Message Queue service: `onsaddr-internet.aliyun.com:80`.

**Note:**

-   The internal Message Queue service does not support cross-region access. For example, if your Realtime Compute for Apache Flink service resides in the China \(Hangzhou\) region but your Message Queue service resides in the China \(Shanghai\) region, Realtime Compute for Apache Flink cannot access this Message Queue service.
-   By default, Realtime Compute for Apache Flink clusters in exclusive mode cannot access the Internet. If you need to access the Internet, configure a NAT gateway.
-   Due to changes in the network security policies of Alibaba Cloud, network connection issues may occur when Realtime Compute for Apache Flink connects to the Internet Message Queue service. We recommend that you use the internal Message Queue service. If an exception occurs when you use the Internet Message Queue service, [submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23). |
|accessID|AccessKey ID|None.|
|accessKey|AccessKey Secret|None.|
|producerGroup|The name of the producer group.|None.|
|tag|The message tag.|Optional. By default, this parameter is empty.|
|fieldDelimiter|The field delimiter.|Optional. Default value: `\u0001`. The delimiter varies based on the following modes: -   In read-only mode, the `\u0001` delimiter is used. `\u0001` is invisible in read-only mode.
-   In edit mode, the `^A` delimiter is used. |
|encoding|The encoding format.|Optional. Default value: `utf-8`.|
|retryTimes|The maximum number of retries for writing data to the table.|Optional. Default value: 10.|
|sleepTimeMs|The retry interval.|Optional. Default value: 1000. Unit: milliseconds.|
|instanceID|The ID of a Message Queue instance.|-   If the Message Queue instance does not have a separate namespace, the instanceID parameter is unavailable.
-   If the Message Queue instance has a separate namespace, the instanceID parameter is required. |

