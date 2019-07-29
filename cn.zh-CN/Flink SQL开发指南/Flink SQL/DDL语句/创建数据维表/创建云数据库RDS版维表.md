# 创建云数据库RDS版维表 {#concept_62532_zh .concept}

本文为您介绍如何创建实时计算云数据库RDS（DRDS）版维表。

**说明：** 实时计算暂不支持引用RDS for MySQL 8.0版本作为数据存储。

## 云数据库RDS版 {#section_awk_yfn_cgb .section}

阿里云关系型数据库（Relational Database Service）简称RDS，是一种稳定可靠、可弹性伸缩的在线数据库服务。基于阿里云分布式文件系统和高性能存储，RDS支持MySQL、SQL Server、PostgreSQL和PPAS（Postgre Plus Advanced Server，一种高度兼容Oracle的数据库）引擎，并且提供了容灾、备份、恢复、监控、迁移等方面的全套解决方案，彻底解决数据库运维的烦恼。

**说明：** 云数据库RDS（DRDS）版插件中的WITH参数一致，可以通用。 在使用云数据库（RDS和DRDS）作为维表时，RDS或DRDS中必须要有真实的表存在。

## 示例 {#section_vm1_2gn_cgb .section}

实时计算支持使用RDS或DRDS作为维表（目前仅支持MySQL数据存储类型），示例代码如下。

``` {#codeblock_siy_bqs_4ed .language-sql}
CREATE TABLE rds_dim_table(
 id INT,
 len INT,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME--定义维表的变化周期，表明该表是一张会变化的表。
) with (
 type='rds',
 url='<yourDatabaseURL>',
 tableName='<yourDatabaseTableName>',
 userName='<yourDatabaseUserName>',
 password='<yourDatabasePassword>'
);
```

**说明：** 声明一个维表时，必须要指名主键。维表JOIN的时候，ON的条件必须包含所有主键的等值条件。RDS或DRDS的主键可以定义为表的主键或是唯一索引列。

## WITH参数 {#section_ll4_pgn_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|url|jdbc连接地址|url的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。内网地址参见如下链接： -   [RDS的内网地址](https://help.aliyun.com/document_detail/26128.html?spm=5176.doc43185.6.581.rxQuNz)
-   [DRDS的内网地址](https://help.aliyun.com/document_detail/56494.html)

 **说明：** 若访问通过VPC访问授权过的RDS，对应的url配置请参见[VPC访问授权](cn.zh-CN/Flink SQL开发指南/数据存储/VPC访问授权.md#)。

 |
|tableName|表名|无|
|userName|用户名|无|
|password|密码|无|
|maxRetryTimes|最大尝试插入次数|可选，默认为3。|

## Cache 参数 {#section_xnd_qgn_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|cache|缓存策略|默认为 `None`, 可选 `LRU`或`ALL`。|
|cacheSize|缓存大小|当选择 `LRU` 缓存策略后，可以设置缓存大小，默认 10000 行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|当选择 `LRU` 缓存策略后，可以设置缓存失效的超时时间，默认不过期。当选择 `ALL` 策略，则为缓存reload 的间隔时间，默认不重新加载。|
|cacheReloadTimeBlackList|缓存策略选择`ALL` 时启用。更新时间黑名单，防止在此时间内做cache 更新。（如双11场景。）|可选，默认为空。自定义输入格式为`2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`。用逗号`,`来分隔多个黑名单，用箭头`->`来分割黑名单的起始结束时间。|

目前RDS/DRDS提供如下三种缓存策略。

-   None：无缓存。
-   LRU：最近使用策略缓存。需要配置相关参数：缓存大小（cacheSize）和 缓存超时时间（cacheTTLMs）。
-   ALL：全量缓存策略。在Job运行前会将远程表中所有数据load到内存中，之后所有的维表查询都会通过cache进行。cache命中不到则不存在，并在缓存过期后重新加载一遍全量缓存。全量缓存策略适合远程表数据量小、miss key多的场景。全量缓存相关配置：缓存更新间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。

**说明：** 

-   因为会异步Reload，使用cache All的时候，需要将维表JOIN的节点增加一些内存，增加的内存大小为远程表两倍的数据量。
-   使用cache All，请特别注意节点的内存，防止内存溢出。

## 测试案例 {#section_d3c_fhn_cgb .section}

``` {#codeblock_x0m_4il_dmz .language-SQL}
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);
create table phoneNumber(
name VARCHAR,
phoneNumber BIGINT,
primary key(name),
PERIOD FOR SYSTEM_TIME--定义维表的变化周期。
)with(
type='rds'
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
t.id
,w.phoneNumber
,t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --维表JOIN时必须指定此声明。
ON t.name = w.name;
```

关于维表详细语法请参见[维表JOIN语句](cn.zh-CN/Flink SQL开发指南/Flink SQL/QUERY语句/维表JOIN语句.md#)。

