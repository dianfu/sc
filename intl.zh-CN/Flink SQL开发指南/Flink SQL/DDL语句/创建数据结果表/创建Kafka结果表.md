# 创建Kafka结果表 {#concept_88918_zh .concept}

本文档为您介绍如何创建实时计算Kafka结果表。

**说明：** 本文档仅适用于独享模式。

## DDL定义 {#section_j1v_npg_cgb .section}

Kafka结果表需要定义的DDL如下。

```language-sql
create table sink_kafka (
    messageKey VARBINARY,
    `message` VARBINARY,
    PRIMARY KEY (messageKey)
) with (
    type = 'kafka010',
    topic = 'yourTopicName',
    bootstrap.servers = 'yourServerAddress'
);
```

**说明：** 

-   创建Kafka结果表时，必须显示的指定primary key \(messageKey\)。
-   实时计算仅在2.2.6及以上版本中，支持阿里云Kafka或自建Kafka的TPS、RPS等指标信息的显示。

## WITH参数 {#section_dj3_bqg_cgb .section}

通用配置

|参数|注释说明|备注|
|--|----|--|
|type|Kafka对应版本|必选，必须是 Kafka08、Kafka09、Kafka010、Kafka011中的一种，版本对应关系参见[Kafka版本对应关系](intl.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据结果表/创建Kafka结果表.md#section_ncs_cqg_cgb)。|
|topic|写入的topic|topic名称|

-   必选配置
    -   Kafka08必选配置:

        |参数|注释说明|备注|
        |--|----|--|
        |zookeeper.connect|zk链接地址|zk连接ID|

    -   Kafka09/Kafka010/Kafka011必选配置：

        |参数|注释说明|备注|
        |--|----|--|
        |bootstrap.servers|Kafka集群地址|Kafka集群地址|

-   可选配置参数

    -   `consumer.id`
    -   `socket.timeout.ms`
    -   `fetch.message.max.bytes`
    -   `num.consumer.fetchers`
    -   `auto.commit.enable`
    -   `auto.commit.interval.ms`
    -   `queued.max.message.chunks`
    -   `rebalance.max.retries`
    -   `fetch.min.bytes`
    -   `fetch.wait.max.ms`
    -   `rebalance.backoff.ms`
    -   `refresh.leader.backoff.ms`
    -   `auto.offset.reset`
    -   `consumer.timeout.ms`
    -   `exclude.internal.topics`
    -   `partition.assignment.strategy`
    -   `client.id`
    -   `zookeeper.session.timeout.ms`
    -   `zookeeper.connection.timeout.ms`
    -   `zookeeper.sync.time.ms`
    -   `offsets.storage`
    -   `offsets.channel.backoff.ms`
    -   `offsets.channel.socket.timeout.ms`
    -   `offsets.commit.max.retries`
    -   `dual.commit.enabled`
    -   `partition.assignment.strategy`
    -   `socket.receive.buffer.bytes`
    -   `fetch.min.bytes`
    **说明：** 其它可选配置项参考Kafka官方文档进行配置。

    -   [Kafka09](https://kafka.apache.org/0110/documentation.html#consumerconfigs)
    -   [Kafka010](https://kafka.apache.org/090/documentation.html#newconsumerconfigs)
    -   [Kafka011](https://kafka.apache.org/0102/documentation.html#newconsumerconfigs)

## Kafka版本对应关系 {#section_ncs_cqg_cgb .section}

|type|Kafka 版本|
|----|--------|
|Kafka08|0.8.22|
|Kafka09|0.9.0.1|
|Kafka010|0.10.2.1|
|Kafka011|0.11.0.2|

## 示例 {#section_gzx_dqg_cgb .section}

```language-sql
create table datahub_input (
id VARCHAR,
nm VARCHAR
) with (
type = 'datahub'
);

create table sink_kafka (
 messageKey VARBINARY,
`message` VARBINARY,
 PRIMARY KEY (messageKey)
) with (
    type = 'kafka010',
    topic = 'yourTopicName',
    bootstrap.servers = 'yourServerAddress'
);


INSERT INTO
    sink_kafka
SELECT
   cast(id as VARBINARY) as messageKey,
   cast(nm as VARBINARY) as `message`
FROM
    datahub_input;
```

