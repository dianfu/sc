# 创建分析型数据库MySQL版3.0维表

本文为您介绍如何创建分析型数据库MySQL版3.0维表、以及创建维表时使用的WITH参数和Cache参数。

**说明：** 本文仅适用于Blink Blink-3.5.0-hotfix及以上版本。

## 语法示例

```
CREATE TABLE dim_ads(
    `name` VARCHAR,
    id VARCHAR,
    PRIMARY KEY (`name`),
    PERIOD FOR SYSTEM_TIME
)with(
    type='ADB30',
    url='jdbc:mysql://<内网地址>/<databaseName>',
    tableName='xxx',
    userName='xxx',
    password='xxx'
);
```

**说明：**

-   在声明一个维表时，必须指明主键。
-   在维表进行JOIN时，ON条件必须包含所有主键的等值条件。
-   分析型数据库MySQL版的主键可以定义为表的主键或唯一索引列。

## WITH参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|type|维表类型|是|固定值为ADB30。|
|url|分析型数据库MySQL版数据库地址|是|分析型数据库MySQL版数据库地址。示例：`url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`。**说明：**

-   分析型数据库MySQL版数据库连接信息，请参见[注册分析型数据库MySQL版](/intl.zh-CN/独享模式/Flink SQL开发指南/数据存储/注册数据存储/注册分析型数据库MySQL版.md)。
-   databaseName：分析型数据库MySQL版数据库名称。 |
|tableName|表名|是|无|
|userName|用户名|是|无|
|password|密码|是|无|
|maxRetryTimes|写入重试次数。|否|默认值为3。|

## CACHE参数

|参数|说明|备注|
|--|--|--|
|cache|缓存策略|目前分析型数据库MySQL版3.0支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表来一条数据，系统会先查找Cache，如果没有找到，则去物理维表中查询。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查询都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：**

-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。
-   对于数据量比较大的维表，选择CACHE ALL时，可能会出现OOM或者Full GC耗时很久的情况，针对这个问题，可以选择以下两种解决方式：
    -   对于支持Cache All策略的维表，开启PartitionedJoin优化。3.6.0版本之前，每个并发默认加载维表全量数据。3.6.0版本之后，CACHE ALL策略支持PartitionedJoin优化。开启PartitionJoin优化后，每个并发只缓存自己并发所需要的数据。
    -   使用HBase或者RDS等Key-Value类型的维表。 |
|cacheSize|缓存大小|当选择LRU缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存更新时间间隔。系统会根据您设置的缓存更新时间间隔，重新加载一次维表中的最新数据，保证源表能JOIN到维表的最新数据。|单位为毫秒。默认不设置此参数，表示不重新加载维表中的新数据。|
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|可选，默认空，格式为'2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'。其中分割符使用情况如下：-   用逗号（,）来分隔多个黑名单。
-   用箭头（-\>）来分割黑名单的起始结束时间。 |
|partitionedJoin|是否开启partitionedJoin。在开启partitionedJoin优化时，主表会在关联维表前，先按照Join KEY进行Shuffle，这样做有以下优点： -   在缓存策略为LRU时，可以提高缓存命中率。
-   在缓存策略为ALL时，节省内存资源，因为每个并发只缓存自己并发所需要的数据。

|默认情况下为false，表示不开启partitionedJoin。 **说明：** 使用partitionedJoin优化前，需要您手动设置partitionedJoin = 'true'。 |

## 代码示例

```
CREATE TABLE datahub_input1 (
  id      BIGINT,
  name    VARCHAR,
  age     BIGINT
) WITH (
  type='datahub'
);

create table phoneNumber (
  name VARCHAR,
  phoneNumber BIGINT,
  primary key(name),
  PERIOD FOR SYSTEM_TIME--维表标识。
) with (
  type='ADB30'
);

CREATE table result_infor (
  id BIGINT,
  phoneNumber BIGINT,
  name VARCHAR
) with (
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

