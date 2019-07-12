# 创建表格存储（Table Store）源表 {#concept_778558 .concept}

本文为您介绍如何创建实时计算表格存储（Table Store）源表。

**说明：** 本文仅适用于实时计算3.2.2及以上版本。

## 什么是表格存储（Table Store） {#section_zg7_baw_drh .section}

表格存储（Table Store）简称OTS，是构建在阿里云飞天分布式系统之上的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发上的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## 表格存储通道服务 {#section_deg_aur_r52 .section}

表格存储通道服务是基于表格存储（Table Store）数据接口的全增量一体化服务，它通过Tunnel Service API和SDK为您提供了增量、全量和增量加全量，3种类型的分布式数据实时消费通道。通过Tunnel Service数据通道，您可以使用流式计算的方式，对表中存量或新增数据的进行消费。实时计算可以将Tunnel Service数据通道作为流式数据的输入，每条数据类似一个JSON格式。Tunnel Service数据通道的示例如下。

``` {#codeblock_5jh_csw_1pe .language-java}
{
            "OtsRecordType": "PUT",  // 数据操作类型，包括PUT、UPDATE和DELETE。
            "OtsRecordTimestamp": 1506416585740836, //数据写入时间（单位为微秒）；全量数据时为0。
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
                    "OtsColumnType": "Put", // 列操作类型，包括PUT、DELETE_ONE_VERSION和DELETE_ALL_VERSION。
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

**说明：** 由于OTS源表中的Primary Key底层可进行Retract撤回机制，OTS源表暂不支持用Event Time作为窗口函数的时间类型。OTS源表仅支持Processing Time作为窗口函数的时间类型。Event Time和Processing Time详细说明，请参见[时间属性](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#)。

## DDL定义 {#section_w29_83r_m3n .section}

实时计算支持使用TableStore作为结果输出。针对本文Tunnel Service数据通道示例的TableStore源表DDL，示例如下。

``` {#codeblock_i9r_eue_gix .language-sql}
create table tablestore_stream(
  pk_1 BIGINT,
  pk_2 VARCHAR,
  attr_0 VARCHAR,
  attr_1 DOUBLE,
  primary key(pk_1, pk_2)
) with (
  type ='ots',
  endPoint ='http://blink-demo.cn-hangzhou.vpc.tablestore.aliyuncs.com',
  instanceName = "blink-demo",
  tableName ='demo_table',
  tunnelName = 'blink-demo-stream',
  accessId ='<yourAccessID>',
  accessKey ='<yourAccessSecret>',
  ignoreDelete = 'false' //是否忽略DELETE操作的数据。
);        
```

## 属性字段 {#section_z37_0ti_j7s .section}

表格存储（Table Store）源表属性字段的获取和使用方法，请参见[获取数据源表属性字段](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据源表/数据源表概述.md#section_v3l_32x_tgb)。

|字段名|注释说明|
|---|----|
|`OtsRecordType`|数据操作类型|
|`OtsRecordTimestamp`|数据操作时间（全量数据时为0）|
|`<列名>_OtsColumnType`|某列的操作类型|

## WITH参数 {#section_4nf_fed_4x5 .section}

|参数|注释说明|备注|
|endPoint|表格存储的实例访问地址|VPC网络环境需要选择实例的VPC Endpoint。|
|instanceName|表格存储的实例名称|无。|
|tableName|表格存储的数据表名|无。|
|tunnelName|表格存储数据表的数据通道名|无。|
|accessId|表格存储读取的accessKey|无。|
|accessKey|表格存储读取的秘钥|无。|
|ignoreDelete|是否忽略DELETE操作类型的实时数据|可选，默认为`false`。|

## CACHE参数 {#section_fwh_gb1_t4g .section}

|参数|注释说明|备注|
|--|----|--|
|cache|缓存策略|默认 `None`, 可选 `LRU`, `ALL`。 -   None：无缓存。
-   LRU：最近使用策略缓存。需要配置cacheSize参数和cacheTTLMs参数。
-   ALL：全量缓存策略。

 |
|cacheSize|缓存大小|选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|默认缓存不超时，单位为毫秒。不同缓存策略下的功能如下： -   LRU：设置缓存失效的超时时长。
-   ALL：设置缓存Reload的间隔时长，默认不重新加载。

 |

