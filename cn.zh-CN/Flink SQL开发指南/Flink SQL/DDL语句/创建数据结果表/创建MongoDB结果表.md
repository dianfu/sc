# 创建MongoDB结果表 {#concept_895199 .concept}

本文为您介绍实时计算MongoDB结果表的DDL定义和WITH参数。

**说明：** 本文仅适用于实时计算3.2.2及以上版本。

## DDL定义 {#section_j4i_wq4_87i .section}

实时计算支持使用MongoDB作为数据输出的结果表，示例代码如下。

``` {#codeblock_f5s_nja_a2x}
CREATE TABLE mongodb_sink (
  `a`             VARCHAR
) WITH (
   type = 'mongodb'
    ,database = '<yourDatabaseName>'
    ,collection= '<yourCollectionName>'
    ,uri='mongodb://{<atabaseAccount>}:{<atabasePassword>}@{host}:****?replicaSet=mgset-1224****'
    ,keepAlive='true'
    ,maxConnectionIdleTime='20000'
    ,batchSize='2000'
);
```

## WITH参数 {#section_rw2_tfn_v1j .section}

|参数|说明|是否必填|备注|
|type|Connector类型|是|固定参数值，`type = 'mongodb'`。|
|database|数据库|是|无。|
|collection|数据集合|是|无。|
|uri|mongodb连接串|是|无。|
|keepAlive|是否保持长连接|否|默认值为true。|
|maxConnectionIdleTime|连接超时时长|否|整型值，单位为毫秒。0表示无连接超时限制，不能为负值，默认值为60000。|
|batchSize|1次批量写入的条数|否|整型值，默认值为1024。|

