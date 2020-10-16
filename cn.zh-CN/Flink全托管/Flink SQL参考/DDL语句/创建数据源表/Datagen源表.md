---
keyword: [Datagen, Datagen源表]
---

# Datagen源表

本文为您介绍Datagen源表的DDL定义、WITH参数和代码示例。

## 什么是Datagen源表

Datagen源表是系统内置的Connector，可以周期性地生成Datagen源表对应类型的随机数据。如果您在注册其他类型的Connector源表时报错，但您不确定是全托管系统问题还是源表WITH参数错误，您可以将WITH参数修改为'connector' = 'datagen'后，单击**运行**。如果不再报错，则证明全托管系统没有问题，您需要确认修改WITH参数。

## DDL定义

```
CREATE TABLE datagen_source (
    name VARCHAR,
    score BIGINT
) WITH (
   'connector' = 'datagen'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型|是|固定值为datagen。|

## 代码示例

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY table datahub_sink(
  name  VARCHAR  
) with (
  'connector'='datahub',
  'endpoint'='<yourEndpoint>',
  'project'='<yourProject>',
  'topic'='<yourTopic>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

