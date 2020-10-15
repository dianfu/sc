---
keyword: [消息队列Kafka, 结果表]
---

# 创建消息队列Kafka结果表

本文档为您介绍如何创建实时计算Flink版消息队列Kafka结果表，以及Kafka版本对应关系。

**说明：**

-   本文仅适用于实时计算2.0及以上版本。
-   本文仅适用于独享模式。
-   Kafka结果表支持写入自建Kafka集群，但需注意版本对应关系，以及自建集群和实时计算集群的网络环境配置。

## 什么是Kafka结果表

消息队列Kafka版是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。实时计算采用Kafka作为流式数据的数据源表或结果表。

## DDL定义

消息队列Kafka结果表需要定义的DDL如下。

```
create table sink_kafka (
  messageKey VARBINARY,
  `message` VARBINARY,
  PRIMARY KEY (messageKey)
) with (
  type = 'kafka010',
  topic = '<yourTopicName>',
  bootstrap.servers = '<yourServerAddress>'
);
```

**说明：**

-   创建Kafka结果表时，必须明文指定`PRIMARY KEY (messageKey)`。
-   仅Blink 2.2.6及以上版本，支持阿里云Kafka或自建Kafka的TPS和RPS等指标信息的显示。

## WITH参数

-   通用配置

    |参数|说明|是否必选|备注|
    |--|--|----|--|
    |type|Kafka对应版本|是|必须是Kafka08、Kafka09、Kafka010、Kafka011中的一种，版本对应关系请参见[Kafka版本对应关系](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建消息队列Kafka结果表.md)。|
    |topic|Topic名称|是|无|

-   必选配置
    -   Kafka08必选配置

        |参数|说明|
        |--|--|
        |zookeeper.connect|zk连接ID|

    -   Kafka09/Kafka010/Kafka011必选配置

        |参数|说明|
        |--|--|
        |bootstrap.servers|Kafka集群地址|

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
    **说明：** 其它可选配置项请参见以下Kafka官方文档：

    -   [Kafka09](https://kafka.apache.org/0110/documentation.html#consumerconfigs)
    -   [Kafka010](https://kafka.apache.org/090/documentation.html#newconsumerconfigs)
    -   [Kafka011](https://kafka.apache.org/0102/documentation.html#newconsumerconfigs)

## Kafka版本对应关系

|type|Kafka版本|
|----|-------|
|Kafka08|0.8.22|
|Kafka09|0.9.0.1|
|Kafka010|0.10.2.1|
|Kafka011|0.11.0.2及以上|

## 示例

```
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
  topic = '<yourTopicName>',
  bootstrap.servers = '<yourServerAddress>'
);

INSERT INTO
  sink_kafka
SELECT
  cast(id as VARBINARY) as messageKey,
  cast(nm as VARBINARY) as `message`
FROM
  datahub_input;
```

