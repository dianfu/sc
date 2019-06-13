# 创建消息队列（RocketMQ）源表 {#concept_627772 .concept}

本文档为您介绍实时计算RocketMQ源表和RocketMQ源表的功能。

**说明：** 本文仅适应于实时计算3.3及以上版本。

## 什么是RocketMQ {#section_ah2_ne6_8v2 .section}

消息队列RocketMQ是阿里巴巴集团自主研发的专业消息中间件，基于高可用分布式集群技术，提供消息订阅和发布、消息轨迹查询以及定时（延时）消息、资源统计、监控报警等一系列消息云服务，是企业级互联网架构的核心产品。

## DDL {#section_pw0_97q_0ew .section}

实时计算需要解析MessageExt的消息getBody\(\) 内容作为流式数据的输入，目前实时计算是对byte\[\]数组进行解析。实时计算消息队列（RocketMQ）源表的DDL示例如下。

``` {#codeblock_xta_9jd_wlg .language-sql}
create table rocketmq_stream(
 x varchar,
 y varchar,
 z varchar
) with (
 type='ROCKETMQ',
 nameserverAddress='127.0.0.1:****',
 consumerTopic='<yourConsumerTopicName>',
 consumerGroup='<yourConsumerGroupName>'
);
```

**说明：** RocketMQ先把读取的byte\[\]数组转换成一个String，然后按照行分隔符切分后，再对每一行数据按列分隔符进行分割，最后按字段顺序进行匹配。WITH参数中的`lineDelimiter`和`fieldDelimiter`需要与消息队列中byte\[\]数组语法一致。

## WITH参数 {#section_bds_rco_xt6 .section}

|参数|注释说明|备注|
|consumerTopic|消费的topic名|必填。|
|consumerGroup|订阅消费group名|必填。|
|nameserverAddress|nameserver所在地址|必填，指向您自行搭建的RocketMQ服务器地址和端口。|
|consumerTag|订阅的标签|可选，默认值为`*`，指适用与所有标签。|
|consumerOffset|消费位点的设置|可选，默认值为latest。支持3种消费位点设置： -   latest：最后的消费位点
-   earliest：最开始的消费位点
-   timestamp：指定时间戳的消费位点

 |
|consumerOffsetFromTimestamp|基于timestamp进行消费|当consumerOffset被设置为timestamp时必须设置该属性。|
|consumerBatchSize|以batch形式对队列进行消费|可选，默认值为32。|
|consumerPullThreadPoolSize|MQPullConsumerScheduleService的线程池大小|可选，默认值为20。|
|consumerDelayWhenMessageNotFound|无消息消费时，触发数据拉取的时间间隔|可选，默认值为10，单位为毫秒。|
|consumerOffsetPersistInterval|持久化consumer消费进度间隔|可选，默认为5000，单位为毫秒。consumerOffsetPersistInterval用于修改RocketMQ原生数据结构DefaultMQPullConsumer的persistConsumerOffsetInterval字段。 **说明：** consumerOffsetPersistInterval用于修改RocketMQ原生数据结构的字段，与checkpoint interval意义不同。

 |
|lineDelimiter|解析消息的行分隔符|可选，默认为 `\n`。 **说明：** 分隔符需要和消息列队数据源中，消息的byte\[\]数组的语法保持一致。

 |
|fieldDelimiter|字段分隔符|可选，默认为`\u0001` 。表示在只读模式下以 `\u0001`（`\u0001`在只读模式不可见）作为分隔符；在编辑模式下以`^A`作为分隔符。 **说明：** 分隔符需要和消息列队数据源中，消息的byte\[\]数组的语法保持一致。

 |
|encoding|编码格式|可选，默认为 `utf-8`。|
|lengthCheck|单行字段条数检查策略|可选，默认值为NONE，表示： -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
-   解析出的字段数小于定义字段数时，跳过这行数据。

 其它可选值为SKIP、EXCEPTION和PAD。 -   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，在行尾用null填充缺少的字段。

 |
|columnErrorDebug|是否打开调试开关，如果打开，会把解析异常的log打印出来|可选，默认为false。如果设置为te，则打印解析异常的log。|

## 类型映射 {#section_umo_xaj_rz1 .section}

|MQ字段类型|实时计算字段类型|
|------|--------|
|STRING|VARCHAR|

## 属性字段 {#section_bl8_qyi_2s1 .section}

RocketMQ默认的属性字段如下。

|属性字段|说明|
|----|--|
|\_\_store\_timestamp\_\_|每条记录入RocketMQ的system time|
|\_\_born\_timestamp\_\_|每条记录产生时的system time，需要在记录产生时进行记录，并在进入RocketMQ前手动设置|
|\_\_queue\_id\_\_|该消息所在队列的id|
|\_\_queue\_offset\_\_|该消息在其所在队列中的offset|

**说明：** 

-   获取属性字段的方法参见[获取数据源表属性字段](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据源表/数据源表概述.md#section_v3l_32x_tgb)。
-   RocketMQ源表支持自定义属性字段，您可以在消息写入消息队列前，设置MessageExt的成员变量。

    ``` {#codeblock_0vs_845_0tc .language-java}
    Map<String, String> properties
    ```


