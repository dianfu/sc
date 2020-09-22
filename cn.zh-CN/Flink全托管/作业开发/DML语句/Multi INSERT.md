---
keyword: [Multi INSERT, TEMPORARY]
---

# Multi INSERT

本文为您介绍如何将DDL和DML写在同一个文本中的语法，即Multi INSERT。

**说明：** Flink全托管控制台不支持直接将创建源表和结果表语句与INSERT INTO语句写在同一个文本。因此，您需要为每个语句新建一个文本。如果您需要将DDL和DML写在同一个文本框简化操作时，您可以在源表和结果表DDL定义语句中，CREATE TABLE中间添加**TEMPORARY**关键字。

```
CREATE TEMPORARY TABLE datagen_source (
  name VARCHAR,
  score BIGINT
)
COMMENT 'datagen source table'  --创建datagen源表时，必填。
WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE blackhole_sink (
  name VARCHAR,
  score BIGINT
)
COMMENT 'blackhole sink table'
WITH (
  'connector' = 'blackhole'  --创建blackhole结果表时，必填。
);

INSERT INTO blackhole_sink 
  SELECT UPPER(name), sum(score) 
  FROM datagen_source 
GROUP BY UPPER(name);
```

