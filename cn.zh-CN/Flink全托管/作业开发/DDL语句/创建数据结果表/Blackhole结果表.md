---
keyword: [Blackhole, Blackhole结果表]
---

# Blackhole结果表

本文为您介绍Blackhole结果表的DDL定义、WITH参数和代码示例。

## 什么是Blackhole结果表

Blackhole结果表是系统内置的Connector。如果您在注册其他类型的Connector结果表时报错，但您不确定是全托管系统问题还是结果表WITH参数错误，您可以将WITH参数修改为'connector' = 'blackhole'后，单击**运行**。如果不再报错，则证明全托管系统没有问题，您需要确认修改WITH参数。

## DDL定义

```
create table blackhole_sink(
  name VARCHAR,
  score BIGINT
)
COMMENT 'blackhole sink table'--必填，blackhole结果表标识。
with (
  'connector' = 'blackhole'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为blackhole。|

## 代码示例

```
CREATE TEMPORARY table datahub_input(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='xxx',
  'project'='xxx',
  'topic'='xxx',
  'subId'='xxx',
  'accessId'='xxx',
  'accessKey'='xxx',
  'startTime'='2018-06-01 00:00:00'
);
CREATE TEMPORARY table test_out(
  name  VARCHAR  
)
COMMENT 'blackhole sink table'--必填，blackhole结果表标识。
with (
  'connector' = 'blackhole'
);

INSERT INTO test_out
SELECT 
  LOWER(name)
from datahub_input;
```

