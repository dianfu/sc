---
keyword: [Redis, 维表]
---

# 创建云数据库Redis维表

本文为您介绍如何创建云数据库Redis维表，以及创建过程中涉及的WITH参数、CACHE参数、类型映射和代码示例。

**说明：**

-   本文仅适用于Blink 3.2.2及以上版本。
-   实时计算Flink版Redis维表仅支持引用Redis数据存储中STRING类型的数据。
-   实时计算Flink版Redis维表支持自建Redis服务。

## 语法示例

实时计算Flink版支持使用Redis作为数据存储维表。Redis维表语法示例如下。

```
CREATE TABLE white_list (
  id VARCHAR,
  name VARCHAR,
  PRIMARY KEY (id),  --Redis中的Row Key字段。
  PERIOD FOR SYSTEM_TIME --维表标识。
) WITH (
  type = 'redis',
  host = '<yourHostName>',
  port = '<yourPort>',
  password = '<yourPassword>',
  dbNum = '<yourDatabaseNumber>'
);
```

**说明：**

-   Redis维表必须声明且只能声明一个主键。
-   维表JOIN时，ON条件必须包含所有主键的等值条件。
-   Redis维表仅支持声明两个字段，且字段类型必须为VARCHAR。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|维表类型|是|固定值为`redis`。|
|host|Redis连接地址|是|无|
|port|Redis连接端口|否|默认值为6379。|
|dbNum|选择操作的数据库|否|默认值为0。|
|password|Redis密码|否|默认值为空，不进行权限验证。|
|hashName|Hash模式下的Hash Key名称|否|默认值为空，实时计算Flink版从Redis中读取STRING类型的数据。 通常，Redis维表中的数据类型为STRING类型，即`key-value`对。如果设置hashName参数，则Redis维表中的数据类型为HASHMAP类型，即`key-{field-value}`对，其中：

-   key为hashName参数值。
-   field为您在CREATE TABLE中指明的key参数值。
-   value为key对应的赋值，和STRING类型`key-value`中value语义相同。 |

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|云数据库Redis维表支持以下两种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表来一条数据，系统会先查找Cache，如果没有找到，则去物理维表中查询。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|否|选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时长|否|默认缓存不超时，单位为毫秒。可选LRU缓存策略，即设置缓存失效的超时时长。|
|cacheEmpty|是否缓存空结果|否|默认值为true。|

## 类型映射

Redis和实时计算Flink版字段类型对应关系如下。建议您使用该对应关系进行DDL声明。

|Redis字段类型|实时计算Flink版字段类型|
|---------|--------------|
|STRING|VARCHAR|

## 代码示例

包含Redis维表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE event (
  id VARCHAR, 
  data VARCHAR) with (
  type = 'random'
);

CREATE TABLE white_list (
  id VARCHAR,
  name VARCHAR,
  PRIMARY KEY (id),  --Redis中的Row Key字段。
  PERIOD FOR SYSTEM_TIME --维表的标识。
) WITH (
  type = 'redis',
  host = '<yourRedisHost>',
  password = '<yourRedisPassword>'
);

SELECT e.*, w.*
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id;
```

