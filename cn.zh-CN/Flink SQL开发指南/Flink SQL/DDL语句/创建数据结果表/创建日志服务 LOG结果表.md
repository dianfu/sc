# 创建日志服务 LOG结果表 {#concept_62529_zh .concept}

本文为您介绍如何创建实时计算日志服务 LOG结果表。

## DDL定义 {#section_ivj_wnf_cgb .section}

实时计算支持使用日志服务作为结果输出。日志服务结果表声明示例如下。

``` {#codeblock_kmd_56v_uzg .language-sql}
create table sls_stream(
 name VARCHAR,
 age BIGINT,
 birthday BIGINT
)with(
 type='sls',
 endPoint='<yourEndpoint>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
 project='<yourProjectName>',
 logstore='<yourLogstoreName>'
);
```

**说明：** 建议使用日志服务存储注册功能，参见[注册日志服务 LOG](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册日志服务 LOG.md#)。

## WITH参数 {#section_njl_xnf_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|endPoint|endPoint地址|[服务入口](../../../../cn.zh-CN/API 参考/服务入口.md#)。|
|project|项目名|无。|
|logstore|logstore表名|无。|
|accessId|accessId|无。|
|accessKey|accessKey|无。|
|mode|写入方式|可选，默认为`random`模式，选择`partition`则会按分区写入。|
|partitionColumn|分区列|如果`mode`为`partition`，则必填。|
|source|日志的来源地，例如产生该日志机器的IP地址。|可选，默认为空。|

