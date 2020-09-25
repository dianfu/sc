---
keyword: [Hologres, 维表, 交互式分析]
---

# 交互式分析Hologres维表

本文为您介绍交互式分析Hologres维表DDL定义、WITH参数、CACHE参数和代码示例。

## 什么是交互式分析Hologres

交互式分析Hologres兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## DDL定义

```
CREATE TABLE dim_table(
 id INT,
 len INT,
 content VARCHAR
) with (
  'connector'='hologres',
  'dbname'='...',
  'tablename'='...',
  'username'='...',
  'password'='...',
  'endpoint'='...'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型|是|固定值为`hologres`（小写）。|
|endPoint|hologres endpoint|是|详情请参见[访问域名](/cn.zh-CN/实例管理/访问域名.md)。|
|tablename|表名|是|无|
|dbname|数据库名|是|无|
|username|用户名，请填写阿里云账号的AccessKey ID。|是|无|
|password|密码，请填写阿里云账号的AccessKey Secret。|否|无|

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|Hologres仅支持以下两种缓存策略：-   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|否|选择`LRU`缓存策略后，可以设置缓存大小，默认值为10000行。|
|cacheTTLMs|缓存更新时间间隔|否|当选择LRU缓存策略后，可以设置缓存失效的超时时间，默认不过期。|
|async|是否异步同步数据。|否|取值如下：-   true：表示异步同步数据。
-   false（默认值）：表示不进行异步同步数据。 |

## 类型映射

|Hologres|Flink全托管|
|--------|--------|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

**说明：** 未列出的Hologres类型，Connector还未支持转换。

## 代码示例

```
CREATE TABLE src(
   a INT,
   b BIGINT,
   c STRING,
   `proc_time` AS PROCTIME()
) with (
   'connector' = 'datagen'
);

CREATE TABLE dim (
   a INT, 
   b VARCHAR, 
   c VARCHAR
) with (
   'connector' = 'hologres',
   ...
);

CREATE TABLE print_sink(
   a INT,
   b STRING
) with (
   'connector' = 'blackhole'
);

insert into print_sink select T.a,H.b
FROM src AS T JOIN dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

