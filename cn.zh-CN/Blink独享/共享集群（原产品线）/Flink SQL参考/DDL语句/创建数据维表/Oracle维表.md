---
keyword: 创建Oracle数据库维表
---

# Oracle维表

本文为您介绍Oracle维表的DDL定义，以及创建维表时使用的WITH参数、类型映射和代码示例。

## DDL定义

```
CREATE TABLE oracle_dim(
  employee_id BIGINT,
  phone_number BIGINT,
  dollar DOUBLE,
  PRIMARY KEY (employee_id)
) WITH (
  'connector' = 'oracle_dim',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>',
  'cache' = 'ALL'
);
```

## WITH 参数

|参数|描述|是否必选|示例值|
|--|--|----|---|
|基本配置|connector|维表类型|是|固定值为oracle\_dim。|
|url|JDBC的Oracle地址|是|`jdbc:oracle:thin:@ip:port:sid`|
|userName|数据库的用户名|是|无|
|password|数据库的密码|是|无|
|tableName|表名称|是|无|
|maxRetryTimes|读取维表异常重试的最大次数|否|默认值为10。|
|缓存配置|cache|缓存策略|否|目前Oracle维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则关键健不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。

**说明：** 因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小，即缓存多少行数据。|否|当缓存策略选择LRU时，可以设置缓存大小，默认值为10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|否|-   当缓存策略选择LRU时，可以设置缓存失效时间，默认不过期。
-   当缓存策略选择ALL时，缓存失效时间为缓存重新加载的间隔时间，默认不重新加载。 |
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|否|默认空，格式为'2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'。分割符如下： -   用逗号（,）来分隔多个黑名单。
-   用箭头（-\>）来分割黑名单的起始结束时间。 |
|maxJoinRows|一对多连接时，左表一条记录连接右表的最大记录数。|否|默认值为1024。**说明：** 一对多连接的记录数过多时，需要调cache的内存。因为cacheSize限制的是左表key个数，单条左表记录对应的右表记录较多时，可能会极大地影响流任务的性能。 |

## 类型映射

|Oracle字段类型|Flink字段类型|
|----------|---------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## 代码示例

```
CREATE TEMPORARY TABLE oracle_source (
  employee_id BIGINT,
  employee_name VARCHAR,
  employee_age INT
) WITH (
  'connector'='datagen'
);


CREATE TEMPORARY TABLE oracle_dim (
  employee_id BIGINT,
  phone_number BIGINT,
  dollar DOUBLE,
  PRIMARY KEY (employee_id)
) WITH (
  'connector' = 'oracle_dim',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>',
  'cache' = 'ALL'
);

CREATE TEMPORARY TABLE oracle_sink (
  employee_id BIGINT,
  phone_number BIGINT,
  employee_name VARCHAR
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);

INSERT INTO oracle_sink
SELECT t.employee_id, w.phone_number, t.employee_name
FROM oracle_source as t JOIN oracle_dim FOR SYSTEM_TIME AS OF PROCTIME() as w
ON t.employee_id = w.employee_id;
```

