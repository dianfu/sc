---
keyword: Phoenix5结果表
---

# 创建Phoenix5结果表

本文为您介绍如何创建实时计算Flink版云数据库Phoenix5结果表。

**说明：**

-   本文仅适用于实时计算Flink版独享模式。
-   本文仅适用于Blink 3.4.0以上版本。
-   Phoenix5仅支持5.x版本。
-   Phoenix5是云数据库HBase实例中的一种HBase SQL服务，须在云数据库HBase实例中开启HBase SQL服务后才可以使用Phoenix5。

## DDL定义

实时计算Flink版支持使用Phoenix5作为结果输出，示例代码如下。

```
create table US_POPULATION_SINK (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY)---主键必填。
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<yourserverUrl>',
  tableName = '<yourTableName>'
);
```

## With参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为`PHOENIX5`。|
|serverUrl|Phoenix5的Query Server地址：-   如果Phoenix5是在集群中创建的，则serverUrl是负载均衡服务的URL地址。
-   如果Phoenix5是在单机中创建的，则serverUrl是单机的URL地址。

|是|您需要在云数据库HBase实例中开启Hbase SQL服务。|
|tableName|读取Phoenix5表名。|是|无|

## 代码示例

包含Phoenix5结果表的实时计算Flink版作业代码示例如下。

```
create table `source` (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar 
) WITH (
   type = 'random'
);

create table sink (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar,
  primary key (id)
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<yourserverUrl>',
  tableName = '<yourTableName>'
);

INSERT INTO sink
  SELECT  `id` ,`name` , `age` ,`birthday` 
FROM `source`;
```

