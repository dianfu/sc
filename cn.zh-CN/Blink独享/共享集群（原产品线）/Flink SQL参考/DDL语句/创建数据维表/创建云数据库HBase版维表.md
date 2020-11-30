---
keyword: [HBase, 维表]
---

# 创建云数据库HBase版维表

本文为您介绍如何创建实时计算云数据库HBase版维表，以及创建维表时使用的WITH参数和CACHE参数。

**说明：**

-   Blink 3.3.0以下版本仅支持HBase企业标准版。
-   Blink 3.3.0及以上版本同时支持HBase企业标准版和HBase性能增强版。
-   Blink 3.5.0及以上版本支持HBase写入主备切换。
-   云数据库HBase版维表的JOIN语法详情请参见[维表JOIN语句](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/QUERY语句/维表JOIN语句.md)。
-   实时计算HBase维表不支持自建的开源HBase。
-   HBase维表仅支持一个PK（Primary Key）。

## DDL定义

-   HBase企业标准版

    ```
    CREATE TABLE hbase (
      `key` varchar, 
      `name` varchar,
      PRIMARY KEY (`key`), --HBase中的rowkey字段。
      PERIOD FOR SYSTEM_TIME --维表标识。
    ) with (
      TYPE = 'cloudhbase',
      zkQuorum = '<yourzkQuorum>',
      columnFamily = '<yourColumnFamilyName>',
      tableName = '<yourTableName>'
    );
    ```

-   HBase性能增强版

    ```
    CREATE TABLE hbase (
      `key` varchar, 
      `name` varchar,
      PRIMARY KEY (`key`), --HBase中的rowkey字段。
      PERIOD FOR SYSTEM_TIME --维表标识。
    ) with (
      TYPE = 'cloudhbase',
      endPoint = '<host:port>',--HBase增强版的Java API访问地址。
      userName  = 'root', --HBase用户名。
      password = 'root', --HBase密码。
      columnFamily = '<yourColumnFamilyName>',
      tableName = '<yourTableName>'
    );
    ```

-   Blink-3.5.0以上HBase性能增强版

    ```
    create table liuxd_user_behavior_test_front (
      row_key varchar,
      from_topic varchar,
      origin_data varchar,
      record_create_time varchar,
      primary key (row_key)
    ) with (
      type = 'cloudhbase',
      zkQuorum = '<host:port>', --HBase增强版的Java API访问地址。
      userName  = 'root', --HBase用户名。
      password = 'root', --HBase密码。
      columnFamily = '<yourColumnFamily>',
      tableName = '<yourTableName>',
      batchSize = '500'
    );
    ```

-   Blink-3.5.0以上支持HBase写入主备切换

    ```
    create table liuxd_user_behavior_test_front (
      row_key varchar,
      from_topic varchar,
      origin_data varchar,
      record_create_time varchar,
      primary key (row_key)
    ) with (
      type = 'cloudhbase',
      zkQuorum = '<host:port>', --HBase高可用访问地址。
      haClusterID = 'ha-xxx', --HBase高可用实例ID。
      userName  = 'root', --HBase用户名。
      password = 'root', --HBase密码。
      columnFamily = '<yourColumnFamily>',
      tableName = '<yourTableName>',
      batchSize = '500' 
    );
    ```


**说明：**

-   在声明维表时，必须要指名主键。
-   在维表进行JOIN时，ON的条件必须包含所有主键的等值条件。示例中HBase中的主键是**row\_key**。
-   HBase企业标准版和HBase性能增强版DDL的区别为连接参数不同：
    -   HBase企业标准版：`zkQuorum`。
    -   HBase性能增强版：`endPoint`。
    -   Blink 3.5.0以上标准版和增强版：`zkQuorum`。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|维表类型|是|固定值为`cloudhbase`。|
|zkQuorum|HBase集群配置的zk地址，是以逗号（,）分隔的主机列表。|是|可以在hbase-site.xml文件中查看hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase企业标准版中生效。 |
|zkNodeParent|集群配置在zk上的路径|否|可以在hbase-site.xml文件中查看hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase企业标准版中生效。 |
|endPoint|HBase地域名称|是|可在购买的HBase实例控制台中获取。 **说明：** 仅在HBase性能增强版中生效。 |
|userName|用户名|否|**说明：** 仅在HBase性能增强版中生效。 |
|password|密码|否|**说明：** 仅在HBase性能增强版中生效。 |
|tableName|HBase表名|是|无|
|columnFamily|列族名|是|仅支持插入同一列族。|
|maxRetryTimes|最大尝试次数|否|默认值为10次。|
|partitionedJoin|是否使用joinKey进行分区。|否|默认值为False。在设置partitionedJoin为True时，使用joinKey进行分区，将数据分发到各JOIN节点，提高缓存命中率。|
|shuffleEmptyKey|是否将上游EMPTY KEY随机发送到下游节点。|否|默认值为True。参数取值如下： -   True：如果上游有多个EMPTY KEY，会将所有EMPTY KEY随机发送到各个JOIN节点。
-   False：如果上游有多个EMPTY KEY，会将所有EMPTY KEY发送至一个JOIN节点。

**说明：** shuffleEmptyKey在partitionedJoin生效后才能使用。 |

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|HBase维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：** 因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小|否|当选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|否|当选择`LRU`缓存策略后，可以设置缓存失效的超时时间，默认不过期。当选择`ALL`策略，则为缓存加载的间隔时间，默认不重新加载。|
|cacheReloadTimeBlackList|缓存策略选择`ALL`时启用。更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|否|默认为空。自定义输入格式如下。 ```
2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00
```

使用逗号（,）分隔多个黑名单，使用箭头（-\>）分割黑名单的起始和结束时间。|
|cacheScanLimit|缓存策略选择`ALL`时启用。读取全量HBase数据，服务端一次RPC返回给客户端的行数。|否|默认值为100条。|

## 代码示例

包含HBase维表的实时计算作业代码示例如下。

```
create table source (
  id   TINYINT,
  name BIGINT
) with (
  type = 'random'
);

create table dim (
  id   TINYINT,
  score BIGINT
  primary key(id),
  PERIOD FOR SYSTEM_TIME
)with(
  type = 'cloudhbase',
  zkQuorum = '<yourzkQuorum>',
  columnFamily = '<yourColumnFamilyName>',
  tableName = '<yourTableName>'
);

CREATE table result_infor(
  id BIGINT,
  score BIGINT
)with(
  type='rds'
);

INSERT INTO result_infor
SELECT
  t.id,
  w.score
FROM source as t
JOIN dim FOR SYSTEM_TIME AS OF PROCTIME() as w
ON t.id = w.id;
```

