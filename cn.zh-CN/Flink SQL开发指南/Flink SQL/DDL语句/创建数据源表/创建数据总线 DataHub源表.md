# 创建数据总线 DataHub源表 {#concept_62522_zh .concept}

本文为您介绍如何为实时计算创建数据总线 DataHub源表以及创建过程涉及到的属性字段、WITH参数和类型映射。

**说明：** DataHub未正式商业。

## 什么是数据总线 {#section_ptb_bvy_bgb .section}

DataHub作为流式数据总线，为阿里云数加平台提供了大数据的入口服务。实时计算可以使用DataHub作为流式数据存储的源头和输出的目的端。

## 示例 {#section_adm_zym_cgb .section}

DataHub可以作为实时计算的数据输入，示例如下。

``` {#codeblock_pir_dkw_9gy .language-sql}
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

## 属性字段 {#section_yyk_rwy_bgb .section}

Flink SQL支持获取DataHub的属性字段。通过读取属性字段可以获得每条信息输入DataHub的系统时间（System Time）。

![System Time](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/62522/cn_zh/1522727303925/1122.png)

|字段名|注释说明|
|---|----|
|timestamp|每条记录写入DataHub的系统时间（System Time）|

**说明：** 获取属性字段的方法参见[获取数据源表属性字段](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据源表/数据源表概述.md#section_v3l_32x_tgb)。

## WITH参数 {#section_dr2_4wy_bgb .section}

目前只支持tuple模式的topic。

|参数|注释说明|备注|
|--|----|--|
|endPoint|消费端点信息|参见[DataHub域名列表](https://help.aliyun.com/document_detail/47442.html?spm=5176.doc47439.6.542.w2TEz3) 。|
|accessId|读取的accessId|无。|
|accessKey|读取的密钥|无。|
|project|读取的项目|无。|
|topic|project下的具体的topic|无。|
|startTime|启动位点的时间|格式为`yyyy-MM-dd hh:mm:ss`。|
|maxRetryTimes|读取最大尝试次数|可选，默认值为20。|
|retryIntervalMs|重试间隔|可选，默认值为1000，单位为毫秒。|
|batchReadSize|单次读取条数|可选，默认值为10，可设置的最大值为1000。|
|lengthCheck|单行字段条数检查策略|可选，默认值为NONE，表示： -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
-   解析出的字段数小于定义字段数时，跳过这行数据。

 其它可选值为SKIP、EXCEPTION和PAD。 -   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，在行尾用null填充缺少的字段。

 |
|columnErrorDebug|是否开启调试功能|可选，默认值为false，表示关闭调试功能。开启调试功能参数值为true，将打印解析异常的日志。|

## 类型映射 {#section_cwm_twy_bgb .section}

DataHub和实时计算字段类型对应关系如下，建议使用该对应关系时进行DDL声明:

|DataHub字段类型|实时计算字段类型|
|-----------|--------|
|BIGINT|BIGINT|
|TIMESTAMP|
|STRING|VARCHAR|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

**说明：** DataHub的TIMESTAMP精确到微秒，在Unix时间戳中为16位，但实时计算定义的TIMESTAMP精确到毫秒，在Unix时间戳中为13位，建议使用BIGINT进行映射。如果需要使用TIMESTAMP，建议使用[计算列](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/计算列.md#)进行转换。

