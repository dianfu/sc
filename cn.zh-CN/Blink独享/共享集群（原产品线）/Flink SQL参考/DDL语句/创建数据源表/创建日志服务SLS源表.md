# 创建日志服务SLS源表

本文为您介绍如何创建日志服务SLS源表，以及创建过程涉及到的属性字段、WITH参数和类型映射。

**说明：** 本文仅适用于Blink 1.4.5及以上版本。

## 什么是日志服务

日志服务SLS是针对日志类数据的一站式服务，对于日志服务而言，数据格式类似JSON，示例如下。

```
{
    "a": 1000,
    "b": 1234,
    "c": "li"
}
```

日志服务本身是流数据存储，实时计算Flink版能将其作为流式数据的输入。

## 语法示例

实时计算Flink版日志服务源表DDL示例如下（代码中的`sls`代表日志服务）。

```
create table sls_stream(
  a INT,
  b INT,
  c VARCHAR
) with (
  type ='sls',  
  endPoint ='http://cn-hangzhou-share.log.aliyuncs.com',
  accessId ='<yourAccessId>',
  accessKey ='<yourAccessKey>',
  startTime = '2017-07-05 00:00:00',
  project ='<yourProjectName>',
  logStore ='<yourLogStoreName>',
  consumerGroup ='<yourConsumerGroupName>'
);
```

## WITH参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|type|源表类型|是|固定值为sls。|
|endPoint|消费端点信息|是|[服务入口](/cn.zh-CN/开发指南/API 参考/服务入口.md)|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|project|读取的SLS项目名称|是|无|
|logStore|Project下的具体的LogStore名称|是|无|
|startTime|日志消费的开始时间|否|无|
|consumerGroup|消费组名|否|您可以自定义消费组名（没有固定格式）。|
|heartBeatIntervalMills|消费客户端心跳间隔时间|否|默认值为10000，单位为毫秒。|
|maxRetryTimes|读取最大尝试次数|否|默认值为5。|
|batchGetSize|单次读取logGroup的条数|否|默认值为100。|
|columnErrorDebug|是否打开调试开关|否|默认值为false，不打开。如果选择打开，则打印解析异常的日志。|
|startupMode|启动消费模式|否|取值如下： -   TIMESTAMP（默认值）：每个Shard从指定时间开始消费。
-   Earliest：每个Shard从最早位置开始消费。
-   Latest：每个Shard从最新位置开始消费。
-   Group\_Offsets：每个Shard优先从服务端保存的Checkpoint开始消费，必须指定consumerGroup。消费模式有以下几种情况：
    -   如果从Flink State中恢复状态成功，则从Flink状态中的Checkpoint开始消费。
    -   如果从Flink State中恢复状态失败：
        -   如果consumerGroup中存在Checkpoint，则尝试从consumerGroup的Checkpoint开始消费。
        -   如果consumerGroup中不存在Checkpoint：
            -   指定了startTime：从startTime开始消费。
            -   未指定startTime：每个shard从最早位置开始消费。

**说明：**

-   仅Blink 3.6.5及以上版本支持该参数。
-   仅当state中不存在Checkpoint时，上述配置才有效。 |

**说明：**

-   实时计算Flink版1.6.0及以下版本，在指定consumerGroup的Shards数目时，可能会影响读取性能，该缺陷正在修复。
-   SLS暂不支持MAP类型的数据。
-   SLS对于不存在字段会置为Null。
-   字段顺序支持无序（建议字段顺序和表中定义一致）。
-   输入数据源为JSON形式时，注意定义分隔符，并且需要采用内置函数[JSON\_VALUE](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/内置函数/字符串函数/JSON_VALUE.md)分析，否则就会解析失败，报错如下。

    ```
    2017-12-25 15:24:43,467 WARN [Topology-0 (1/1)] com.alibaba.blink.streaming.connectors.common.source.parse.DefaultSourceCollector - Field missing error, table column number: 3, data column number: 3, data filed number: 1, data: [{"lg_order_code":"LP00000005","activity_code":"TEST_CODE1","occur_time":"2017-12-10 00:00:01"}]                
    ```

-   batchGetSize设置不能超过1000，否则会报错。
-   batchGetSize指明的是logGroup获取的数量。如果单条logItem的大小和batchGetSize都很大，有可能会导致频繁的垃圾回收（Garbage Collection），这种情况下该参数应调小。

## 类型映射

日志服务和实时计算Flink版字段类型对应关系如下。建议您使用该对应关系进行DDL声明。

|日志服务字段类型|实时计算Flink版字段类型|
|--------|--------------|
|STRING|VARCHAR|

## 属性字段

目前Flink SQL默认支持3个SLS属性字段的获取，也支持其它自定义字段的写入。属性字段使用方法请参见[获取数据源表属性字段](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/数据源表概述.md)。

|字段名|注释说明|
|---|----|
|`__source__`|消息源|
|`__topic__`|消息主题|
|`__timestamp__`|日志时间|

## 代码示例

```
create table sls_input(
  a int, 
  b int,
  c varchar
) with (
  type ='sls',
  endPoint ='http://cn-hangzhou-share.log.aliyuncs.com',
  accessId ='<yourAccessI>',
  accessKey ='<yourAccessKey>',
  startTime = '2017-07-05 00:00:00',
  project ='ali-cloud-streamtest',
  logStore ='stream-test',
  consumerGroup ='consumerGroupTest1'
);

create table print_output(
 a int,
 b int,
 c varchar 
) with (
  type='print'
);

INSERT INTO print_output
SELECT 
  a, b, c
from sls_input;
```

## 常见问题

-   Q：为什么Job的整体延迟增加，或有窗口聚合的Job无输出？

    A：如果一个Partition没有新数据写入，会导致上述情况，只需要把并发数调整为读写的Partition数量即可。

-   Q：如何设置并发数？

    A：并发数建议等于Partition数量，否则当两个Partition读取速度差异较大时，理论上在追数据场景，存在数据被过滤掉和数据延迟的风险。

-   Q：Flink Job延迟增大应该如何排查？

    A：SLS源表可能会发生Shard分裂，分裂后的Shard index会不连续，导致Flink延迟增大。如果发现Flink Job延迟增大，请查看SLS源是否发生分裂。

-   Q：如何获取属性字段？

    A：属性字段获取方法，请参见[获取数据源表属性字段](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/数据源表概述.md)。

    **说明：** 本地调试时无法抽取到属性字段数据，建议您使用线上调试方法，在日志中进行查看，详情请参见[线上调试](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调试/线上调试.md)。


## 相关文档

-   日志服务帮助文档，请参见[什么是日志服务](/cn.zh-CN/产品简介/什么是日志服务.md)。
-   日志服务中实时计算消费文档，请参见[实时计算（Blink）消费](/cn.zh-CN/消费与投递/实时消费/实时计算（Blink）消费.md)。

