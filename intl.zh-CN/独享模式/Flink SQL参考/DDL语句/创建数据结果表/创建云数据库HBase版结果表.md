---
keyword: [HBase, 结果表]
---

# 创建云数据库HBase版结果表

本文为您介绍如何创建实时计算云数据库HBase版结果表。

**说明：**

-   本文档仅适用于实时计算独享模式。
-   Blink 3.3.0以下版本仅支持HBase标准版。
-   Blink 3.3.0及以上版本同时支持HBase标准版和HBase增强版。
-   Blink 3.5.0及以上版本支持HBase写入主备切换。
-   实时计算HBase结果表不支持自建的开源HBase。

## DDL定义

实时计算支持使用HBase作为结果输出。

-   HBase标准版示例代码如下。

    ```
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
    );
    ```

-   HBase增强版示例代码如下。

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        endPoint = '<host:port>', ----HBase增强版的Java API访问地址。
        userName  = 'root', --用户名。
        password = 'root', --密码。
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500'
    );
    ```

-   Blink 3.5.0以上HBase增强版示例代码如下。

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        zkQuorum = '<host:port>', ----HBase增强版的Java API访问地址。
        userName  = 'root', --用户名。
        password = 'root', --密码。
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500'
    );
    ```

-   Blink 3.5.0以上HBase写入主备切换示例代码如下。

    ```
    create table liuxd_user_behavior_test_front (
        row_key varchar,
        from_topic varchar,
        origin_data varchar,
        record_create_time varchar,
        primary key (row_key)
    ) with (
        type = 'cloudhbase',
        zkQuorum = '<host:port>', ----HBase高可用访问地址。
        haClusterID = 'ha-xxx', ----HBase高可用实例ID。
        userName  = 'root', --用户名。
        password = 'root', --密码。
        columnFamily = '<yourColumnFamily>',
        tableName = '<yourTableName>',
        batchSize = '500' 
    );
    ```


**说明：**

-   PRIMARY KEY支持定义多字段。多字段以`rowkeyDelimiter`（默认为`:`）作为分隔符进行连接。
-   HBase执行撤回删除操作时，如果COLUMN定义了多版本，将清空所有版本的COLUMN值。
-   HBase标准版和HBase增强版DDL的区别为连接参数不同：
    -   HBase标准版使用连接参数`zkQuorum`。
    -   HBase增强版使用连接参数`endPoint`。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|zkQuorum|HBase集群配置的zk地址|是|可以在hbase-site.xml文件中查看hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase标准版中生效。 |
|zkNodeParent|集群配置在zk上的路径|否|可以在hbase-site.xm文件中查看hbase.zookeeper.quorum相关配置。 **说明：** 仅在HBase标准版中生效。 |
|endPoint|HBase地域名称|是|可在购买的HBase实例控制台中获取。 **说明：** 仅在HBase增强版中生效。 |
|userName|用户名|否|**说明：** 仅在HBase增强版中生效。 |
|password|密码|否|**说明：** 仅在HBase增强版中生效。 |
|tableName|HBase表名|是|无|
|columnFamily|列族名|是|仅支持插入同一列族。|
|maxRetryTimes|最大尝试次数|否|默认值为10。**说明：** 仅实时计算3.2.3及以上版本支持maxRetryTimes参数。 |
|bufferSize|流入多少条数据后进行去重|否|默认值为5000。|
|batchSize|一次批量写入的条数|否|默认值为100。**说明：** 建议batchSize参数值为200~300。过大的batchSize值可能导致任务OOM（内存不足）报错。 |
|flushIntervalMs|周期性清理buffer的间隔，可以减少写入HBase的延迟|否|默认值为2000，单位为毫秒。|
|writePkValue|是否写入主键值|否|默认值为false。|
|stringWriteMod|是否都按照STRING插入|否|默认值为false。|
|rowkeyDelimiter|rowKey的分隔符|否|默认值为冒号（:）。|
|isDynamicTable|是否为动态表|否|默认值为false。|
|haClustserID|HBase高可用实例ID|否|只有访问同城主备实例时才需要配置。|

## 动态表

实时计算部分结果数据需要按某列的值，作为动态列输入HBase。HBase中，以每小时的成交额作为动态列的数据，示例如下。

|rowkey|cf:0|cf:1|cf:2|
|------|----|----|----|
|20170707|100|cf:1|300|

当isDynamicTable参数值为true时，表明该表为支持动态列的HBase表。

动态表仅支持3列输出，例如，ROW\_KEY、COLUMN和VALUE。此时第2列（本示例中的COLUMN）为动态列，动态表中的其它参数与HBase的WITH参数一致。

**说明：** 使用动态表时，所有数据类型需要先转换为STRING类型，再进行输入。

```
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
);
```

**说明：**

-   以上声明将把`birthday`插入到以`name`为ROW\_KEY的`cf:age`列中。例如，`（wang,18,2016-12-12)`会插入ROW\_KEY为`wang`的行，`cf:18`列。
-   DDL中必选按照从上到下的顺序，声明ROW\_KEY（本示例中的`name`）、COLUMN（本示例中的`age`）和VALUE（本示例中的`birthday`），且声明ROW\_KEY为PRIMARY KEY。

## 代码示例

包含HBase结果表的实时计算作业代码示例如下。

```
create table source (
  id   TINYINT,
  name BIGINT
) with (
  type = 'random'
);

create table sink (
  id    TINYINT,
  name  BIGINT,
  primary key (id)
) with (
  type = 'cloudhbase',
  zkQuorum = '<yourZkQuorum>',  
  columnFamily = '<yourColumnFamily>',
  tableName = '<yourTableName>'
);

INSERT INTO sink
SELECT id, name FROM source;
```

## 常见问题

Q：为什么HBase结果表作业运行时会报错`cloudHbase update error, No columns to insert for #10 item`？

A：HBase结果表要求写入的单条记录的列数据（不包括rowkey）不能全为NULL。请先过滤全为NULL的数据，再输入HBase。

