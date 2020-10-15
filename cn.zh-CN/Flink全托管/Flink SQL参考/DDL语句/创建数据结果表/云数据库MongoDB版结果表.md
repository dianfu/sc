---
keyword: [MongoDB, 结果表]
---

# 云数据库MongoDB版结果表

本文为您介绍云数据库MongoDB版结果表DDL定义、WITH参数和代码示例。

**说明：** 仅支持将MongoDB作为结果表使用。

## DDL定义

```
CREATE TABLE mongodb_table(
   id INT, 
   number INT
) with (
   'connector' = 'mongodb',
   'database' = '<yourDatabase>',
   'collection' = '<yourCollection>', 
   'uri' = '<yourUri>',
   'maxConnectionIdleTime' = '<yourMaxConnectionIdleTime>',  
   'batchSize' = '1024'  
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为`mongodb`。|
|database|数据库名称|是|无|
|collection|数据集合|是|无|
|uri|MongoDB连接串|是|例如`mongodb://123@dds-/admin?replicaSet=mgset-32966591`。|
|maxConnectionIdleTime|连接超时时长|否|默认值为60000。|
|batchSize|每次批量写入的条数|否|默认值为1024。|

## 代码示例

```
CREATE TABLE datagen_stream (
   v INT, 
   p INT
) with (
   'connector' = 'datagen'
);

CREATE TABLE mongodb_table(
   id INT, 
   number INT
) with (
   'connector'='mongodb',
   'database' = '<yourDatabase>',
   'collection' = '<yourCollection>', 
   'uri'='<yourUri>'
);

INSERT INTO mongodb_table 
   SELECT v, p
FROM datagen_stream;
```

