# 创建云数据库 HBase版维表 {#concept_vdl_bkn_cgb .concept}

本文为您介绍如何创建实时计算云数据库 HBase版维表。

**说明：** 

-   blink-3.3.0以下版本仅支持HBase企业标准版
-   blink-3.3.0及以上版本同时支持HBase企业标准版和HBase性能增强版。
-   云数据库 HBase版维表的JOIN语法详见： [维表JOIN语句](cn.zh-CN/Flink SQL开发指南/Flink SQL/QUERY语句/维表JOIN语句.md#)。

## 示例 {#section_lmd_qkn_cgb .section}

-   HBase企业标准版示例代码如下。

    ``` {#codeblock_3go_e43_4kk .language-sql}
    CREATE TABLE hbase (
       `key` varchar, 
       `name` varchar,
        PRIMARY KEY (`key`), -- HBase中的rowkey字段。
        PERIOD FOR SYSTEM_TIME --维表标识。
       ) with (
        TYPE = 'cloudhbase',
        zkQuorum = '<yourzkQuorum>',
        columnFamily = '<yourColumnFamilyName>',
        tableName = '<yourTableName>'
    );
    ```

-   HBase性能增强版示例代码如下。

    ``` {#codeblock_7ip_n8u_u9r .language-sql}
    CREATE TABLE hbase (
       `key` varchar, 
       `name` varchar,
        PRIMARY KEY (`key`), -- HBase中的rowkey字段。
        PERIOD FOR SYSTEM_TIME --维表标识。
       ) with (
        TYPE = 'cloudhbase',
        endPoint = '<yourEndpoint>',
        columnFamily = '<yourColumnFamilyName>',
        tableName = '<yourTableName>'
    );
    ```


**说明：** 

-   声明维表时，必须要指名主键。维表JOIN的时候，ON的条件必须包含所有主键的等值条件。HBase中的主键即rowkey。
-   HBase企业标准版和HBase性能增强版DDL的区别为连接参数不同：
    -   HBase企业标准版使用连接参数`zkQuorum`。
    -   HBase性能增强版使用连接参数`endPoint`。

## 参数 {#section_ldg_cln_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|zkQuorum|HBase集群配置的zk地址，是以`,`分隔的主机列表。|可以在hbase-site.xml文件中找到hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase企业标准版中生效。

 |
|zkNodeParent|集群配置在zk上的路径|可以在hbase-site.xm文件中找到hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase企业标准版中生效。

 |
|endPoint|HBase地域名称|可在购买的HBase实例控制台中获取。 **说明：** 仅在HBase性能增强版中生效。

 |
|userName|用户名|可选。 **说明：** 仅在HBase性能增强版中生效。

 |
|password|密码|可选。 **说明：** 仅在HBase性能增强版中生效。

 |
|tableName|HBase表名|无。|
|columnFamily|列族名|目前只支持插入同一列族。|
|userName|用户名|无。|
|password|密码|无 。|
|maxRetryTimes|最大尝试次数|默认10次。|
|partitionedJoin|设置为true之后会在用joinKey做partition，将数据分发到join节点，提高缓存命中率。|可选，默认关闭。|
|shuffleEmptyKey|设置为true之后遇到空key会随机往下游做shuffle，否则往0号下游发。|建议打开。|

## CACHE参数 {#section_v2w_fmn_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|cache|缓存策略|默认 `None`，可选 `LRU`，`ALL`。|
|cacheSize|缓存大小|当选择 `LRU` 缓存策略后，可以设置缓存大小，默认 10000 行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|当选择 `LRU` 缓存策略后，可以设置缓存失效的超时时间，默认不过期。当选择 `ALL` 策略，则为缓存reload 的间隔时间，默认不重新加载。|
|cacheReloadTimeBlackList|缓存策略选择`ALL`时启用。更新时间黑名单，防止在此时间内做cache更新。（如双11场景。）|可选，默认为空。自定义输入格式为 ``` {#codeblock_a1m_337_m0y}
为2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11
            08:00
```

 。用逗号`,`来分隔多个黑名单，用箭头`->`来分割黑名单的起始结束时间。|
|cacheScanLimit|缓存策略选择`ALL`时启用。load全量HBase数据，服务端一次RPC返回给客户端的行数。|可选，默认100条。|

目前RDS和DRDS提供如下三种缓存策略。

-   None：无缓存。
-   LRU：最近使用策略缓存。需要配置相关参数：缓存大小（cacheSize）和 缓存超时时间（cacheTTLMs）。
-   ALL：全量缓存策略。在Job运行前会将远程表中所有数据load到内存中，之后所有的维表查询都会通过cache进行。cache命中不到，则会在缓存过期后重新加载一遍全量缓存。全量缓存策略适合远程表数据量小、miss key多的场景。全量缓存相关配置：缓存更新间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。

    **说明：** 因为会异步reload，使用cache all的时候，需要将维表JOIN的节点增加一些内存，增加的内存大小为远程表2倍的数据量。


