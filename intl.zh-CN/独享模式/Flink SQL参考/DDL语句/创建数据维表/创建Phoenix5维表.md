---
keyword: [维表, Phoenix5]
---

# 创建Phoenix5维表

本文为您介绍如何创建实时计算Flink版Phoenix5维表，以及创建维表时使用的WITH参数和CACHE参数。

**说明：** 仅Blink 3.4.0以上版本支持Phoenix5维表。

## 语法示例

```
create table US_POPULATION_DIM (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY),
  PERIOD FOR SYSTEM_TIME
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<YourServerUrl>',
  tableName = '<YourTableName>'
);           
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|维表类型|是|固定值为`PHOENIX5`。|
|serverUrl|Phoenix5的Query Server地址： -   如果Phoenix5是在集群中创建的，则serverUrl是负载均衡服务的URL地址。
-   如果Phoenix5是在单机中创建的，则serverUrl是单机的URL地址。

|是|您需要在云数据库HBase实例中开启Hbase SQL服务。|
|tableName|Phoenix5表名|是|无|

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|目前Phoenix5维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查询都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：** 因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小|否|选择LRU缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|否|当选择LRU缓存策略后，可以设置缓存失效的超时时间，默认不过期。当选择ALL策略，则为缓存加载的间隔时间，默认不重新加载。|
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|否|可选，默认空，格式为'2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'。用逗号（,）来分隔多个黑名单，用箭头（-\>）来分割黑名单的起始或结束时间。|

## 代码示例

```
CREATE TABLE datahub_input1 (
  id  BIGINT,
  name  VARCHAR,
  age   BIGINT
) WITH (
  type='datahub'
);

create table phoneNumber(
  name VARCHAR,
  phoneNumber BIGINT,
  primary key(name),
  PERIOD FOR SYSTEM_TIME--定义维表的变化周期。
)with(
  type='PHOENIX5'
);

CREATE table result_infor(
  id BIGINT,
  phoneNumber BIGINT,
  name VARCHAR
)with(
  type='rds'
);

INSERT INTO result_infor
SELECT
  t.id,
  w.phoneNumber,
  t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --维表JOIN时必须指定该声明。
ON t.name = w.name;
```

