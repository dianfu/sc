# 创建表格存储Tablestore结果表

本文为您介绍如何创建实时计算Flink版表格存储结果表，以及表格存储和实时计算Flink版字段类型之间的映射关系。

**说明：** 本文仅适用于Blink 1.4.5及以上版本。

## 什么是表格存储Tablestore

表格存储Tablestore是基于阿里云飞天分布式系统的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发上的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## DDL定义

实时计算Flink版支持使用Tablestore作为结果输出，示例代码如下。

```
CREATE TABLE stream_test_hotline_agent (
 name VARCHAR,
 age BIGINT,
 birthday BIGINT,
 PRIMARY KEY (name,age)
) WITH (
 type='ots',
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessSecret>',
 endPoint='<yourEndpoint>',
 valueColumns='birthday'
); 
```

**说明：**

-   推荐使用数据存储注册功能，详情请参见[注册表格存储Tablestore](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/注册数据存储/注册表格存储Tablestore.md)。
-   valueColumns值不能是声明的主键，可以是主键之外的任意字段。
-   Tablestore结果表声明中，除主键列外，至少包含一个属性列。

## WITH参数

|参数|说明|备注|
|--|--|--|
|type|结果表类型|固定值为ots。|
|instanceName|实例名|无|
|tableName|表名|无|
|endPoint|实例访问地址|参见[服务地址](/cn.zh-CN/功能介绍/基础概念/服务地址.md)。|
|accessId|AccessKey ID|无|
|accessKey|AccessKey Secret|无|
|valueColumns|指定插入的字段列名|插入多个字段以英文逗号（,）分割。例如`'ID,NAME'`。|
|bufferSize|流入多少条数据后开始输出|可选，默认值为5000，表示输入的数据达到5000条就开始输出。 **说明：** 在实时计算Flink版系统，bufferSize根据Tablestore主键对结果数据进行去重后，再在bufferSize的基础上进行batchSize。 |
|batchWriteTimeoutMs|写入超时的时间|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|batchSize|一次批量写入的条数|可选，默认值为100。|
|retryIntervalMs|重试间隔时间|可选，单位毫秒，默认值为1000。|
|maxRetryTimes|最大重试次数|可选，默认值为100。|
|ignoreDelete|是否忽略DELETE操作|默认值为False。|

## 类型映射

|Tablestore字段类型|实时计算Flink版字段类型|
|--------------|--------------|
|INTEGER|BIGINT|
|STRING|VARCHAR|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE|

**说明：** Tablestore结果表必须定义有`Primary Key`，以Update方式写入结果数据到Tablestore表。Update方式说明请参见[Update类型](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/数据结果表概述.md)。

