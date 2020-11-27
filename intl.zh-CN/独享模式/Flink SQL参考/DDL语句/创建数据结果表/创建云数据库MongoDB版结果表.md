---
keyword: [MongoDB, 结果表]
---

# 创建云数据库MongoDB版结果表

本文为您介绍如何创建实时计算Flink版云数据库MongoDB版结果表，以及创建过程中涉及到的WITH参数。

**说明：**

-   本文仅适用于Blink 3.2.2及以上版本。
-   MongoDB结果表不支持主键更新，数据输入形式为重复插入。

## DDL定义

实时计算Flink版支持使用MongoDB作为数据输出的结果表，示例代码如下。

```
CREATE TABLE mongodb_sink (
  `a`             VARCHAR
) WITH (
   type = 'mongodb',
   database = '<yourDatabaseName>',
   collection= '<yourCollectionName>'
   uri='mongodb://{<databaseAccount>}:{<atabasePassword>}@{host}:****?replicaSet=mgset-1224****',
   keepAlive='true',
   maxConnectionIdleTime='20000',
   batchSize='2000'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|Connector类型|是|固定值为mongodb。|
|database|数据库名称|是|无|
|collection|数据集合|是|无|
|uri|MongoDB连接串|是|无|
|keepAlive|是否保持长连接|否|默认值为true。|
|maxConnectionIdleTime|连接超时时长|否|整型值，不能为负数，单位为毫秒。默认值为60000。0表示无连接超时限制。|
|batchSize|每次批量写入的条数|否|整型值，默认值为1024。系统会设定一个大小为batchSize的缓冲条数，当数据的条数达到batchSize时，触发数据的输出。 **说明：** 当Checkpoint时间达到时，即使数据未到达batchSize值，也将触发数据的输出。 |

