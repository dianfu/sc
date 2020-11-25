---
keyword: [Message Queue for Apache Kafka, result table]
---

# Create a Message Queue for Apache Kafka result table

This topic describes how to create a Message Queue for Apache Kafka result table in Realtime Compute for Apache Flink. It also describes the mapping between the values of the type parameter and Kafka versions.

**Note:**

-   This topic applies only to Realtime Compute for Apache Flink V2.0 and later.
-   This topic applies only to Realtime Compute for Apache Flink in exclusive mode.
-   Data of a Message Queue for Apache Kafka result table can be written into a self-managed Kafka cluster. Before data is written, you must pay attention to the mapping between the values of the type parameter and Kafka versions, and the network configurations of the self-managed Kafka cluster and the Realtime Compute for Apache Flink cluster.

## Introduction to the Message Queue for Apache Kafka result table

Message Queue for Apache Kafka is a distributed, high-throughput, and scalable message queue service provided by Alibaba Cloud. This service is widely used in big data fields, such as log collection, monitoring data aggregation, streaming data processing, and online and offline data analysis. Realtime Compute for Apache Flink allows you to create source tables and result tables of Message Queue for Apache Kafka for the processing of streaming data.

## DDL syntax

The following example demonstrates how to create a Message Queue for Apache Kafka result table in a data definition language \(DDL\) statement.

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

**Note:**

-   When you create a Message Queue for Apache Kafka result table, you must specify `PRIMARY KEY (messageKey)` in plaintext mode.
-   Only Blink 2.2.6 and later versions support the display of metrics such as transactions per second \(TPS\) and requests per second \(RPS\) of Alibaba Cloud Message Queue for Apache Kafka or a self-managed Kafka database.

## Parameters in the WITH clause

-   General configurations

    |Parameter|Description|Required|Remarks|
    |---------|-----------|--------|-------|
    |type|The Kafka version.|Yes|Valid values: Kafka08, Kafka09, Kafka010, and Kafka011. For more information, see [Mapping between the values of the type parameter and Kafka versions](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create a Message Queue for Apache Kafka result table.md).|
    |topic|The name of the topic.|Yes|None.|

-   Required configurations
    -   Required configurations for Kafka08

        |Parameter|Description|
        |---------|-----------|
        |zookeeper.connect|The ZooKeeper URL.|

    -   Required configurations for Kafka09, Kafka010, and Kafka011

        |Parameter|Description|
        |---------|-----------|
        |bootstrap.servers|The Kafka cluster address.|

-   Optional configurations

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
    **Note:** For more information about optional configuration items, see the following official Kafka documentation:

    -   [Kafka09](https://kafka.apache.org/0110/documentation.html#consumerconfigs)
    -   [Kafka010](https://kafka.apache.org/090/documentation.html#newconsumerconfigs)
    -   [Kafka011](https://kafka.apache.org/0102/documentation.html#newconsumerconfigs)

## Mapping between the values of the type parameter and Kafka versions

|type|Kafka version|
|----|-------------|
|Kafka08|0.8.22|
|Kafka09|0.9.0.1|
|Kafka010|0.10.2.1|
|Kafka011|0.11.0.2 and later|

## Example

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

