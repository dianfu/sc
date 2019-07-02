# 创建消息队列（MQ）结果表 {#concept_62528_zh .concept}

本文为您介绍如何创建实时计算消息队列（MQ）结果表。

## 什么是消息列队（MQ） {#section_jnb_3qf_cgb .section}

消息队列（Message Queue）简称MQ，是阿里云商用的专业消息中间件，是企业级互联网架构的核心产品。消息列队是基于高可用分布式集群技术，搭建了包括发布订阅、消息轨迹、资源统计、定时（延时）、监控报警等一套完整的消息云服务。 实时计算可以将消息队列作为流式数据输出，如下所示。

``` {#codeblock_0uh_mlh_xfs .language-sql}
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

## CSV类格式 {#section_tvd_lqg_chb .section}

``` {#codeblock_3l5_tqa_3q6 .language-sql}
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

## 二进制格式 {#section_xw5_5qg_chb .section}

二进制格式测试代码如下。

``` {#codeblock_ov6_noy_vcu .language-sql}
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
FROM source_table
```

**说明：** `cast(varchar as varbinary)`需在实时计算2.0及以上版本使用，若版本低于2.0，请先完成版本升级。

## WITH参数 {#section_glb_mqf_cgb .section}

|参数|说明|备注|
|--|--|--|
|topic|Message Queue队列名称|无|
|endpoint|地址| -   公共云内网接入（阿里云经典网络/VPC）：华东1、华东2、华北1、华北2、华南1香港的区域的endpoint地址为`onsaddr-internal.aliyun.com:8080` 。
-   公共云公网接入地址为`http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet`。

 |
|accessID|填写阿里云accessID|无|
|accessKey|填写阿里云accessKey|无|
|producerGroup|写入的群组|无|
|tag|写入的标签|可选，默认为空。|
|fieldDelimiter|字段分割符|可选，默认为`\u0001` 。表示在只读模式下以 `\u0001`（`\u0001`在只读模式不可见）作为分隔符；在编辑模式下以`^A`作为分隔符。|
|encoding|编码类型|可选，默认为`utf-8`。|
|retryTimes|写入的重试次数|可选，默认为10。|
|sleepTimeMs|重试间隔时间|可选，默认为1000（毫秒）。|
|instanceID|Topic所属的分组|实时计算3.2及以上版本必须。|

