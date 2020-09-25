---
keyword: [消息队列, 结果表, MQ]
---

# 消息队列RocketMQ版结果表

本文为您介绍消息队列RocketMQ版结果表DDL定义、WITH参数和示例代码等。

## 什么是消息队列RocketMQ版

消息队列 RocketMQ版是阿里云基于Apache RocketMQ构建的低延迟、高并发、高可用和高可靠的分布式消息中间件。消息队列RocketMQ版既可为分布式应用系统提供异步解耦和削峰填谷的能力，同时也具备互联网应用所需的海量消息堆积、高吞吐和可靠重试等特性。Flink全托管支持将消息队列MQ作为流式数据的输出。

## DDL定义

```
create table mq_output(
   x varchar,
   y varchar,
   z varchar
) with (
   'connector'='mq',
   'topic'='<yourTopicName>',
   'endpoint'='<yourEndpoint>',
   'pullIntervalMs'='1000',
   'accessId'='<yourAccessId>',
   'accessKey'='<yourAccessSecret>',
   'startMessageOffset'='1000',
   'consumerGroup'='<yourConsumerGroup>',
   'fieldDelimiter'='|'
);
```

**说明：** MQ是非结构化存储格式的消息中间件，对于数据的Schema不提供强制定义，完全由业务层指定。Flink全托管仅支持CSV和二进制格式的MQ消息。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为`mq`。|
|topic|topic名称|是|无|
|endpoint|地址|是|仅支持公网地域的MQ，接入地址为`onsaddr-internet.aliyun.com:80`。|
|accessID|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|producerGroup|写入的群组|是|无|
|tag|写入的标签|否|默认值为空。|
|fieldDelimiter|字段分割符|否|默认值为`\u0001` 。分隔符的使用情况如下所示： -   只读模式：以 `\u0001`作为分隔符，`\u0001`在只读模式不可见。
-   编辑模式：以`^A`作为分隔符。 |
|encoding|编码类型|否|默认值为`utf-8`。|
|retryTimes|写入的重试次数|否|默认值为10。|
|sleepTimeMs|重试间隔时间|否|默认值为1000（毫秒）。|
|instanceID|Topic所属的分组|否|-   如果MQ实例无独立命名空间，则不可以使用instanceID参数。
-   如果MQ实例有独立命名空间，则instanceID参数必选。 |

## 代码示例

-   CSV格式

    ```
    create table stream_test_hotline_agent (
        id INTEGER,
        len BIGINT,
        content VARCHAR
    ) with (
        'connector'='mq',
        'endpoint'='<yourEndpoint>',
        'accessId'='<yourAccessId>',
        'accessKey'='<yourAccessSecret>',
        'topic'='<yourTopicName>',
        'producerGroup'='<yourGroupName>',
        'tag'='<yourTagName>',
        'encoding'='utf-8',
        'fieldDelimiter'=',',
        'retryTimes'='5',
        'sleepTimeMs'='500'
    );
    ```

-   二进制格式

    ```
    CREATE TABLE source_table (
        commodity VARCHAR
    ) WIHT (
        'connector'='random'
    );
    
    CREATE TABLE result_table (
        mess VARBINARY
    ) WITH (
        'connector'='mq',
        'endpoint'='<yourEndpoint>',
        'accessId'='<yourAccessId>',
        'accessKey'='<yourAccessSecret>',
        'topic'='<yourTopicName>',
        'producerGroup'='<yourGroupName>'
    );
    
    INSERT INTO result_table
    SELECT 
         CAST(SUBSTRING(commodity,0,5) AS VARBINARY) AS mess   
    FROM source_table;
    ```


