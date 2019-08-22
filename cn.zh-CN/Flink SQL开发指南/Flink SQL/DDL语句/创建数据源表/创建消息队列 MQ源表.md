# 创建消息队列 MQ源表 {#concept_62523_zh .concept}

本文为您介绍实时计算创建消息队列 MQ源表以及创建过程涉及到的CSV类格式、WITH参数和类型映射。

## 什么是消息队列MQ {#section_ezv_dkz_bgb .section}

消息队列 MQ是阿里云专业消息中间件，是企业级互联网架构的核心产品。实时计算可以将消息队列作为流式数据输入。

## 示例 {#section_h3x_mkz_bgb .section}

``` {#codeblock_ef7_xj8_8t4 .language-sql}
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

**说明：** MQ实际上是一个非结构化存储格式，对于数据的Schema不提供强制定义，完全由业务层指定。目前实时计算支持类CSV格式文本和二进制格式。

## CSV类格式 {#section_h3m_bsg_chb .section}

假设您的1条CSV格式消息记录如下。

``` {#codeblock_o3e_p6v_6in}
1,name,male 
2,name,female
```

**说明：** 1条MQ消息可以包括0条到多条数据记录，记录之间使用`\n`分隔。

在实时计算作业中，声明MQ数据源表的DDL如下。

``` {#codeblock_sak_jxa_6z7 .language-sql}
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

## 二进制格式 {#section_xw5_5qg_chb .section}

二进制格式测试代码如下。

``` {#codeblock_6br_s9a_2cl .language-sql}
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
)with(
  type='print'
);
INSERT INTO out_table
SELECT 
  cast(mess as varchar)
FROM source_table
```

**说明：** 

-   `cast(mess as varbinary)`需在实时计算2.0及以上版本使用，若版本低于2.0，请先升级。
-   VARBINARY只能入参一次。

## WITH参数 {#section_nnh_4kz_bgb .section}

|参数|注释说明|备注|
|--|----|--|
|topic|topic名称|无。|
|endPoint|endPoint地址| -   公共云内网（阿里云经典网络/VPC）接入地址：
    -   华东1、华东2、华北1华北2、华南1、中国（香港）：`onsaddr-internal.aliyun.com:8080`
    -   新加坡：`ap-southeastaddr-internal.aliyun.com:8080`
    -   迪拜：`ons-me-east-1-internal.aliyuncs.com:8080`
    -   孟买：`ons-ap-south-1-internal.aliyuncs.com:8080`
    -   吉隆坡：`ons-ap-southeast-3-internal.aliyun.com:8080`
-   公共云公网接入地址：`onsaddr-internet.aliyun.com:80`

 |
|accessId|accessId|无。|
|accessKey|accessKey|无。|
|consumerGroup|订阅消费group名称|无。|
|pullIntervalMs|拉取时间间隔|单位为毫秒。|
|startTime|消息消费启动的时间点|可选。|
|startMessageOffset|消息开始的偏移量|可选，如果填写，将优先以startMessageoffset的位点开始加载。|
|tag|订阅的标签|可选。|
|lineDelimiter|解析block时行分隔符|可选，默认为 `\n`。|
|fieldDelimiter|字段分隔符|可选，默认为`\u0001` 。表示在只读模式下以 `\u0001`（`\u0001`在只读模式不可见）作为分隔符；在编辑模式下以`^A`作为分隔符。|
|encoding|编码格式|可选，默认为 `utf-8`。|
|lengthCheck|单行字段条数检查策略|可选，默认值为NONE，表示： -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
-   解析出的字段数小于定义字段数时，跳过这行数据。

 其它可选值为SKIP、EXCEPTION和PAD。 -   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，在行尾用null填充缺少的字段。

 |
|columnErrorDebug|是否打开调试开关|可选，默认为false。如果设置为true，则打印解析异常的log。|
|instanceID|Topic所属的分组|可选。|

## 类型映射 {#section_hkx_4kz_bgb .section}

|MQ字段类型|实时计算字段类型|
|------|--------|
|STRING|VARCHAR|

