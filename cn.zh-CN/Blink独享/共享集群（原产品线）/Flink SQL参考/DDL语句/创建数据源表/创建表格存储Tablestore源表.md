---
keyword: [表格存储, Tablestore源表]
---

# 创建表格存储Tablestore源表

本文为您介绍如何创建实时计算Flink版表格存储Tablestore源表。

**说明：** 本文仅适用于实时计算Flink版3.2.2及以上版本。

## 什么是表格存储Tablestore

表格存储Tablestore是构建在阿里云飞天分布式系统之上的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发上的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## 表格存储通道服务

表格存储通道服务是基于表格存储数据接口的全增量一体化服务，通过Tunnel Service API和SDK，为您提供了增量、全量和增量加全量三种类型的分布式数据实时消费通道。通过Tunnel Service数据通道，您可以使用流式计算的方式，消费表中存量或新增数据。实时计算Flink版可以将Tunnel Service数据通道作为流式数据的输入，每条数据类似一个JSON格式。Tunnel Service数据通道的示例如下。

```
{
            "OtsRecordType": "PUT",  // 数据操作类型，包括PUT、UPDATE和DELETE。
            "OtsRecordTimestamp": 1506416585740836, //数据写入时间（单位为微秒），全量数据时为0。
            "PrimaryKey": [
                {
                    "ColumnName": "pk_1", //第1主键列。
                    "Value": 1506416585881590900
                },
                {
                    "ColumnName": "pk_2", //第2主键列。
                    "Value": "string_pk_value"
                }
            ],
            "Columns": [
                {
                    "OtsColumnType": "PUT", // 列操作类型，包括PUT、DELETE_ONE_VERSION和DELETE_ALL_VERSION。
                    "ColumnName": "attr_0",
                    "Value": "hello_table_store",
                },
                {
                    "OtsColumnType": "DELETE_ONE_VERSION", // DELETE操作没有Value字段。
                    "ColumnName": "attr_1"
                }
            ]
}
```

## DDL定义

实时计算Flink版支持使用Tablestore作为源表，示例代码如下。

```
create table tablestore_stream(
  pk_1 BIGINT,
  pk_2 VARCHAR,
  attr_0 VARCHAR,
  attr_1 DOUBLE,
  OtsRecordType  VARCHAR  HEADER //属性字段后边需要增加HEADER。
) with (
  type ='ots',
  endPoint ='http://blink-demo.cn-hangzhou.vpc.tablestore.aliyuncs.com',
  instanceName = 'blink-demo',
  tableName ='demo_table',
  tunnelName = 'blink-demo-stream',
  accessId ='<yourAccessID>',
  accessKey ='<yourAccessSecret>',
  ignoreDelete = 'false' //是否忽略DELETE操作的数据。
);        
```

## 属性字段

表格存储源表属性字段的获取和使用方法，请参见[获取数据源表属性字段](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/数据源表概述.md)。

|字段名|说明|
|---|--|
|`OtsRecordType`|数据操作类型|
|`OtsRecordTimestamp`|数据操作时间（全量数据时，`OtsRecordTimestamp`为0）|
|`<列名>_OtsColumnType`|某列的操作类型|

## WITH参数

|参数|说明|备注|
|--|--|--|
|type|connector类型|固定值为`ots`。|
|endPoint|表格存储的实例访问地址|VPC网络环境需要选择实例的VPC Endpoint。|
|instanceName|表格存储的实例名称|无|
|tableName|表格存储的数据表名|实时计算读取Tablestore源表数据时，已读取的数据不会再被读取，如果您有再次读取全量数据的需求，则需要重新创建新的数据通道。|
|tunnelName|表格存储数据表的数据通道名|无|
|accessId|表格存储读取的AccessKey ID|无|
|accessKey|表格存储读取的密钥AccessKey Secret|无|
|ignoreDelete|是否忽略DELETE操作的数据。|可选，默认值为`false`。|

