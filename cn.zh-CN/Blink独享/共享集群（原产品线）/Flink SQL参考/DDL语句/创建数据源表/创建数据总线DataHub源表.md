# 创建数据总线DataHub源表

本文为您介绍如何创建数据总线DataHub源表以及创建过程涉及到的属性字段、WITH参数和类型映射。

**说明：** 本文仅适用于Blink 1.4.5及以上版本。

## 什么是数据总线

阿里云流数据处理平台DataHub是流式数据（Streaming Data）的处理平台，提供对流式数据的发布（Publish）、订阅（Subscribe）和分发功能，让您可以轻松构建基于流式数据的分析和应用。实时计算Flink版使用DataHub作为流式数据存储源头或输出目的端。

## 语法示例

DataHub可以作为实时计算Flink版的数据输入，示例如下。

```
CREATE TABLE datahub_stream(
  name VARCHAR,
  age BIGINT,
  birthday BIGINT
) WITH (
  type='datahub',
  endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
  project='<yourProjectName>',
  topic='<yourTopic>',
  accessId='<yourAccessID>',
  accessKey='<yourAccessSecret>',
  startTime='2017-07-21 00:00:00'
); 
```

**说明：** 建议您使用存储注册功能，详情请参见[注册数据总线DataHub](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/注册数据存储/注册数据总线DataHub.md)。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|源表类型|是|固定值为`datahub`。|
|endPoint|消费端点信息|是|详情请参见[DataHub域名列表](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC)。|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|project|读取的项目|是|无|
|topic|Project下的具体的Topic名称|是|无**说明：** 目前只支持Tuple模式的Topic。 |
|startTime|启动位点的时间|否|批处理时必填，格式为`yyyy-MM-dd hh:mm:ss`。|
|endTime|DataHub日志结束时间|否|批处理时必填，格式为`yyyy-MM-dd hh:mm:ss`。|
|maxRetryTimes|读取最大重试次数|否|不同版本中，该参数的默认值如下： -   Blink 2.2.7以下版本：3
-   Blink 2.2.7及以上版本：20 |
|retryIntervalMs|重试间隔|否|不同版本中，该参数的默认值如下（单位为毫秒）： -   Blink 2.2.7以下版本：1000
-   Blink 2.2.7及以上版本：50 |
|batchReadSize|单次读取条数|否|默认值为10，可设置的最大值为1000。|
|lengthCheck|单行字段条数检查策略|否|-   NONE（默认值）：
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，跳过这行数据。
-   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。

**说明：**

    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，按从左到右的顺序，在行尾用Null填充缺少的字段。 |
|columnErrorDebug|是否开启调试功能|否|-   false（默认值）：关闭调试功能。
-   true：开启调试功能，将打印解析异常的日志。 |
|isBlob|DataHub是否为BLOB类型|否|默认值为false。**说明：**

-   仅Blink 3.4.x 及以上版本支持该参数。
-   使用BLOB类型时，字段需要声明为VARBINARY类型，与METAQ类似。 |

## 类型映射

DataHub和实时计算Flink版字段类型对应关系如下，建议使用该对应关系时进行DDL声明。

|DataHub字段类型|实时计算Flink版字段类型|
|-----------|--------------|
|BIGINT|BIGINT|
|TIMESTAMP|
|STRING|VARCHAR|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

## 属性字段

Flink SQL支持获取DataHub的属性字段。通过读取属性字段可以获得每条信息输入DataHub的系统时间（System Time）。

|字段名|说明|
|---|--|
|timestamp|每条记录写入DataHub的系统时间（System Time）|

![Shard数据抽样](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6084359951/p64847.png)

**说明：** 获取属性字段的方法，请参见[获取数据源表属性字段](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/数据源表概述.md)。

## 示例代码

包含DataHub源表的实时计算Flink版作业代码示例如下。

```
create table datahub_input(
  name VARCHAR
) with (
  type='datahub',
  endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
  project='test1',
  topic='topic1',
  accessId='<yourAccessID>',
  accessKey='<yourAccessSecret>',
  startTime='2018-06-01 00:00:00'
);

create table test_out(
 name  VARCHAR  
) with (
  type='print'
);

INSERT INTO test_out
SELECT 
  LOWER(name)
from datahub_input;
```

## 常见问题

-   Q：DataHub和实时计算Flink版中的TIMESTAMP的精确级别不同，如何进行转换？

    A：DataHub的TIMESTAMP是精确到微秒，在Unix时间戳里是16位，但实时计算Flink版定义的TIMESTAMP是精确到毫秒，在Unix时间戳里是13位，所以建议您使用BIGINT映射。如果您需要使用TIMESTAMP，建议使用计算列进行转换，详情请参见[计算列](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/基本概念/计算列.md)，代码示例如下。

    ```
    CREATE TABLE datahub_test (
      id                 VARCHAR,
      `type`             VARCHAR,
      `value`            DOUBLE,
      `time`             BIGINT,
       ts as TO_TIMESTAMP(`time`/1000),
       WATERMARK wk for ts as withoffset(ts,2000)
    ) WITH (
      type = 'datahub'
    );
    ```

-   Q：分裂或者缩容DataHub Topic后导致实时计算Flink版作业失败，如何恢复？

    A：Blink 2.2.0以下版本不支持DataHub Shard扩容和缩容功能，如果分裂或者缩容了某个实时计算正在读取的Topic，会导致任务持续出错，无法自行恢复。该情况下需要重新启动（停止-\>启动）来使任务恢复正常。

-   Q：可以删除正在使用的DataHub Topic吗？

    A：所有版本均不支持删除或重建正在引用的DataHub Topic。


