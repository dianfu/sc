---
keyword: [日志服务, 结果表]
---

# 创建日志服务SLS结果表

本文为您介绍如何创建实时计算Flink版日志服务SLS结果表。

**说明：**

-   本文仅适用于Blink 1.4.5及以上版本。
-   日志服务SLS结果表仅支持VARCHAR类型的字段。

## 什么是日志服务

日志服务SLS是针对日志类数据的一站式服务。日志服务可以帮助您快捷地完成数据采集、消费、投递以及查询分析，提升运维和运营效率，建立海量日志处理能力。日志服务本身是流数据存储，实时计算Flink版能将其作为流式数据的输入。

## DDL定义

实时计算Flink版支持使用日志服务作为结果输出。日志服务结果表声明示例如下。

```
create table sls_stream(
 `name` VARCHAR,
 age BIGINT,
 birthday BIGINT
)with(
 type='sls',
 endPoint='http://cn-hangzhou-corp.sls.aliyuncs.com',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
 project='<yourProjectName>',
 logstore='<yourLogstoreName>'
);
```

**说明：** 建议使用日志服务存储注册功能，详情请参见[注册日志服务SLS](/intl.zh-CN/独享模式/Flink SQL开发指南/数据存储/注册数据存储/注册日志服务SLS.md)。

## WITH参数

|参数|注释说明|是否必填|备注|
|--|----|----|--|
|endPoint|EndPoint地址|是|[服务入口](/intl.zh-CN/开发指南/API 参考/服务入口.md)|
|project|项目名|是|无|
|logstore|表名|是|无|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|topic|属性字段|否|默认值为空，可以将选定的字段作为属性字段`topic`填充。|
|timestampColumn|属性字段|否|默认值为空，可以将选定的字段作为属性字段`timestamp`填充（类型必须为INT）。如果未指定，则默认填充当前时间。 **说明：** 实时计算Flink版2.2.2及以上版本支持该参数。 |
|source|属性字段。日志的来源地，例如产生该日志机器的IP地址。|否|默认值为空，可以将选定的字段作为属性字段`source`填充。|
|partitionColumn|分区列|否|如果`mode`为`partition`，则该参数必填。|
|flushIntervalMs|触发数据写入的周期|否|默认值为2000，单位为毫秒。|
|reserveMilliSecond|TIMESTAMP类型是否保留毫秒值。|否|默认值为false，不保留。 **说明：** 实时计算Flink版2.2.6及以上版本支持该参数。 |

## 类型映射

日志服务和实时计算Flink版字段类型对应关系如下。建议您使用该对应关系进行DDL声明。

|日志服务字段类型|实时计算Flink版字段类型|
|--------|--------------|
|STRING|VARCHAR|

## 代码示例

包含日志服务SLS结果表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE random_input (
  a VARCHAR, 
  b VARCHAR) with (
    type = 'random'
);

create table sls_output(
 a varchar,
 b varchar
)with(
 type='sls',
 endPoint='http://cn-hangzhou-corp.sls.aliyuncs.com',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
 project='ali-cloud-streamtest',
 logStore='stream-test2'
);

INSERT INTO sls_output
SELECT a, b
FROM random_input;
```

## 常见问题

Q：如何配置输出结果表中的topic？

A：需要配置topic为结果表中的一个字段。例如，本文示例代码中设置`topic='age'`。配置完成后，结果值中`age`字段的值会写入日志服务，同时日志服务不会把`age`字段写入下游。

## 相关文档

-   日志服务帮助文档，请参见[什么是日志服务](/intl.zh-CN/产品简介/什么是日志服务.md)。
-   日志服务中实时计算消费文档，请参见[实时计算（Blink）消费](/intl.zh-CN/消费与投递/实时消费/实时计算（Blink）消费.md)。

