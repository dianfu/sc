# 创建表格存储Tablestore维表

本文为您介绍如何创建实时计算Flink版表格存储Tablestore维表。

**说明：** 本文仅适用于Blink 1.4.5及以上版本。

## 什么是表格存储Tablestore

表格存储Tablestore是构建在阿里云飞天分布式系统之上的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发上的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## 示例

实时计算Flink版支持表格存储Tablestore作为维表，示例如下。

```
CREATE TABLE ots_dim_table (
 id int,
 len int,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME--定义了维表的变化周期。
) WITH (
 type='ots',
 endPoint='<yourEndpoint>',
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>'
);
```

**说明：**

-   在声明维表时，必须要指名主键。
-   在维表JOIN时，ON条件必须包含所有主键的等值条件。
-   Tablestore的主键即表的Rowkey。

## WITH参数

|参数|说明|备注|
|--|--|--|
|type|维表类型|固定值为`ots`。|
|endPoint|表格存储的实例访问地址|VPC网络环境需要选择实例的VPC Endpoint。|
|instanceName|表格存储的实例名称|无|
|tableName|表格存储的数据表名|无|
|accessId|表格存储读取的AccessKey ID|无|
|accessKey|表格存储读取的密钥AccessKey Secret|无|

## CACHE参数

|参数|说明|备注|
|--|--|--|
|cache|缓存策略|表格存储维表支持以下两种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表来一条数据，系统会先查找Cache，如果没有找到，则去物理维表中查询。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|当选择LRU缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|当选择LRU缓存策略后，可以设置缓存失效的超时时间。|

## 代码示例

```
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

CREATE TABLE phoneNumber(
name VARCHAR,
phoneNumber bigint,
primary key(name),
PERIOD FOR SYSTEM_TIME--维表标识。
)with(
type='ots'
);

CREATE TABLE result_infor(
id bigint,
phoneNumber bigint,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id,
w.phoneNumber,
t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --维表JOIN时必须指定此声明。
ON t.name = w.name;     
```

维表的详细语法请参见[维表JOIN语句](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/QUERY语句/维表JOIN语句.md)。

