---
keyword: Phoenix5结果表
---

# Phoenix5结果表

本文为您介绍Phoenix5结果表DDL定义、WITH参数和代码示例。

## DDL定义

```
create table US_POPULATION_SINK (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY)
) WITH (
  'connector' = 'PHOENIX5',
  'serverUrl' = '<yourserverUrl>',
  'tableName' = '<yourTableName>',
  'batchsize' = '25'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|默认值为`PHOENIX5`。|
|serverUrl|Phoenix5的Query Server地址：-   如果Phoenix5是在集群中创建的，则serverUrl是负载均衡服务的URL地址。
-   如果Phoenix5是在单机中创建的，则serverUrl是单机的URL地址。

|是|您需要在云数据库HBase实例中开启Hbase SQL服务，详情请参见[开启HBase SQL服务]()。|
|tableName|Phoenix5表名|是|无|
|batchSize|一次批量写入的条数|否|默认值为20。|

## 代码示例

包含Phoenix5结果表的Flink全托管作业代码示例如下。

```
create table `source` (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar 
) 
COMMENT 'datagen source table' --必填，Datagen源表标识。
WITH (
  'connector' = 'datagen'
);

create table sink (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar,
  primary key (id)
) WITH (
  'connector' = 'phoenix5',
  'serverurl' = '<yourserverUrl>',
  'tablename' = '<yourTableName>',
  'batchsize' = '25'
);

INSERT INTO sink
  SELECT  `id` ,`name` , `age` ,`birthday` 
FROM `source`;
```

