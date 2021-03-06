---
keyword: [消息队列Kafka, 结果表]
---

# 消息队列Kafka结果表

本文为您介绍消息队列Kafka结果表的DDL定义、WITH参数和示例。

## 什么是消息队列Kafka

消息队列Kafka版是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。**Flink全托管**支持将其作为流式数据的输出。

## DDL定义

```
create table kafkaTable(  
  user_id BIGINT,
  item_id BIGINT,
  category_id BIGINT,
  behavior STRING,
  ts TIMESTAMP(3)        
) with (
  'connector' = 'kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourKafkaBrokers>',
  'format' = 'csv'
);
```

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|结果表类型|是|STRING|固定值为`kafka`。|
|topic|结果表对应的Topic|是|STRING|无|
|properties.bootstrap.servers|Kafka Broker地址|是|STRING|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|format|Flink Kafka Connector在反序列化来自Kafka的消息时使用的格式。|是|STRING|格式取值如下：-   csv
-   json
-   avro
-   debezium-json
-   canal-json |
|scan.startup.mode|Kafka消费启动位点|否|STRING|启动位点取值如下：-   group\_offsets（默认值）：根据Group读取。
-   earliest\_offset：从Kafka最早分区开始读取。
-   latest\_offset：从Kafka最新位点开始读取。
-   timestamp：从Kafka指定时间点读取。

需要在WITH参数中指定scan.startup.timestamp-millis参数。

-   specific\_offsets：从Kafka指定分区指定偏移量读取。

需要在WITH参数中指定scan.startup.specific-offsets参数。 |
|scan.startup.specific-offsets|在specific-offsets启动模式下，指定每个分区的启动偏移量。|否|STRING|例如：partition:0,offset:42;partition:1,offset:300|
|scan.startup.timestamp-millis|在timestamp启动模式下，指定启动位点时间戳。|否|LONG|单位为毫秒。|
|sink.partitioner|从Flink分区到Kafka分区的映射模式。|否|STRING|映射模式取值如下：-   fixed：每个Flink分区对应至多一个Kafka分区。
-   round-robin：Flink分区中的数据将被轮流分配至Kafka的各个分区。
-   自定义FlinkKafkaPartitioner的子类：如果fixed和round-robin不满足您的需求，您可以自定义映射模式到FlinkKafkaPartitioner的子类。例如`org.mycompany.MyPartitioner`。 |

如果您还需要直接配置Connector使用的Kafka Producer，可以在Kafka Producer配置参数前添加`properties`前缀，并将该Kafka Producer配置信息追加至WITH参数。例如Kafka集群需要SASL（Simple Authentication and Security Layer）认证。

```
CREATE TABLE kafkaTable (
    ...
) WITH (
    ...
    'properties.security.protocol' = 'SASL_PLAINTEXT',
    'properties.sasl.mechanism' = 'PLAIN',
    'properties.sasl.jaas.config' = 'org.apache.kafka.common.security.plain.PlainLoginModule required username="USERNAME" password="PASSWORD";'
);
```

Kafka Producer配置项详情请参见[Producer Configs](https://kafka.apache.org/documentation/#producerconfigs)。

## 从Kafka中读取数据后插入Kafka示例

从名称为source的Topic中读取Kafka数据，再写入名称为sink的Topic，数据使用CSV格式。

```
CREATE TABLE KafkaSourceTable (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = 'source',
    'properties.bootstrap.servers' = 'kafka1:9092,kafka2:9092,kafka3:9092',
    'properties.group.id' = 'mygroup',
    'format' = 'csv'
);

CREATE TABLE KafkaSinkTable (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = 'sink',
    'properties.bootstrap.servers' = 'kafka1:9092,kafka2:9092,kafka3:9092',
    'format' = 'csv'
);

INSERT INTO KafkaSinkTable SELECT (id, name, age) FROM KafkaSourceTable;
```

