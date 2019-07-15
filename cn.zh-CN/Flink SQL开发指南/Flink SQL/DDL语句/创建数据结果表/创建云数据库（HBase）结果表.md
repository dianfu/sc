# 创建云数据库（HBase）结果表 {#concept_91464_zh .concept}

本文为您介绍如何创建实时计算云数据库（HBase）结果表。

**说明：** 本文档仅适用于实时计算独享模式。

## DDL定义 {#section_l2m_q1g_cgb .section}

实时计算支持使用HBase作为结果输出。示例代码如下。

``` {#codeblock_rua_osa_a1v}
create table liuxd_user_behavior_test_front (
    row_key varchar,
    from_topic varchar,
    origin_data varchar,
    record_create_time varchar,
    primary key (row_key)
) with (
    type = 'cloudhbase',
    zkQuorum = '2',  
    columnFamily = '<yourColumnFamily>',
    tableName = '<yourTableName>',
    batchSize = '500'
)    
```

**说明：** 

-   PRIMARY KEY支持定义多字段。多字段以`rowkeyDelimiter`（默认为`:`）作为分隔符进行连接。
-   HBase执行撤回删除操作时，如果COLUMN定义了多版本，将清空所有版本的COLUMN值。

## WITH参数 {#section_zdt_m1g_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|zkQuorum|HBase集群配置的zk地址|可以在hbase-site.xml文件中找到hbase.zookeeper.quorum相关配置。|
|zkNodeParent|集群配置在zk上的路径|可以在hbase-site.xml文件中找到hbase.zookeeper.quorum相关配置。|
|tableName|HBase表名|无。|
|userName|用户名|无。|
|password|密码|无。|
|partitionBy|是否使用joinKey进行分区|可选，默认为false。设置为true时，使用joinKey进行分区，将数据分发到各JOIN节点，提高缓存命中率。|
|shuffleEmptyKey|是否将上游EMPTY KEY随机发送到下游节点|可选，默认为false。参数意义如下： -   false：如果上游有多个EMPTY KEY，将会将所有EMPTY KEY发送至一个JOIN节点。
-   true：如果上游有多个EMPTY KEY，将会将所有EMPTY KEY随机发送到各个JOIN节点。

 **说明：** shuffleEmptyKey在partitionedJoin生效后才是使用。

 |
|columnFamily|列族名|目前只支持插入同一列族。|
|maxRetryTimes|最大尝试次数|可选，默认为10。 **说明：** 仅实时计算3.2.3及以上版本支持maxRetryTimes参数。

 |
|bufferSize|流入多少条数据后进行去重|默认为5000。|
|batchSize|一次批量写入的条数|可选，默认为100。 **说明：** 建议batchSize参数值为200~300。过大的batchSize值可能导致任务OOM（内存不足）报错。

 |
|flushIntervalMs|最长插入时间|可选，默认为2000。|
|writePkValue|是否写入主键值|可选，默认为false。|
|stringWriteMod|是否都按照string插入|可选，默认为false。|
|rowkeyDelimiter|rowKey的分隔符|可选，默认为`:`。|
|isDynamicTable|是否为动态表|可选，默认为false。|

## 动态表 {#section_3k6_x0r_gtp .section}

实时计算部分结果数据需要按某列的值，作为动态列输入HBase。HBase中，以每小时的成交额作为动态列的数据，示例如下。

|rowkey|cf:0|cf:1|...|
|------|----|----|---|
|20170707|100|200|...|

当isDynamicTable参数值为true时，表明该表为支持动态列的HBase表。

动态表仅支持3列输出，例如，ROW\_KEY, COLUMN和VALUE。此时第2列（本示例中的COLUMN）为动态列，动态表中的其它参数与HBase的WITH参数一致。

**说明：** 使用动态表时，所有数据类型需要先转换为STRING类型，再进行输入。

``` {#codeblock_c5q_hwy_ko9 .language-SQL}
CREATE TABLE stream_test_hotline_agent (
  name varchar,
  age varchar,
  birthday varchar,
  primary key (name)
) WITH (
  type = 'cloudhbase',
  ...
columnFamily = 'cf',
 isDynamicTable ='true'
)
```

**说明：** 

-   以上声明将把`birthday`插入到以`name`为ROW\_KEY的`cf:age`列中。例如，`（wang,18,2016-12-12)`会插入ROW\_KEY为`wang`的行，`cf:18`列。
-   DDL中必选按照从上到下的顺序，声明ROW\_KEY（本示例中的`name`）、COLUMN（本示例中的`age`）和VALUE（本示例中的`birthday`），且声明ROW\_KEY为PRIMARY KEY。

