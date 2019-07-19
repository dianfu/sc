# 创建数据总线 DataHub结果表 {#concept_62530_zh .concept}

本文为您介绍如何创建实时计算数据总线 DataHub结果表。

## 什么是数据总线 DataHub {#section_xmt_2w2_cgb .section}

DataHub作为流式数据总线，为阿里云数加平台提供了大数据的入口服务。实时计算通常使用DataHub作为流式数据存储输入源和输出目的端。

## DDL定义 {#section_ocn_lx2_cgb .section}

实时计算支持使用DataHub作为数据输出结果表，DataHub结果表声明示例如下。

``` {#codeblock_12p_ic0_73z .language-sql}
create table datahub_output(
  name VARCHAR,
  age BIGINT,
  birthday BIGINT
)with(
  type='datahub',
  endPoint='<yourEndpoint>,
  project='<yourProjectName>',
  topic='<yourTopicName>',
  accessId='<yourAccessId>',
  accessKey='<yourAccessKey>',
  batchSize='<yourBatchSize>',
  batchWriteTimeoutMs='1000'
);
			
```

**说明：** 建议使用存储注册功能，参见[ZH-CN\_TP\_40854.md\#](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册数据总线 DataHub.md#)。

## WITH参数 {#section_pvr_nx2_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|endPoint|Endpoint地址|参见[DataHub的Endpoint地址](https://help.aliyun.com/document_detail/47442.html?spm=5176.doc47439.6.542.w2TEz3)。|
|project|DataHub项目名称|无|
|topic|DataHub中topic名称|无|
|accessId|accessId|无|
|accessKey|accessKey|无|
|maxRetryTimes|最大重试次数|可选，默认值为3。|
|batchSize|一次批量写入的条数|可选，默认值为300。|
|batchWriteTimeoutMs|缓存数据的超时时间|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|maxBlockMessages|每次写入的最大Block数|可选，默认值为100。|

