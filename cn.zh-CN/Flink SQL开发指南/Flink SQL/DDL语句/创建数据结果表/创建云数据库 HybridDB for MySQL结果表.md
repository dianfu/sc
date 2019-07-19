# 创建云数据库 HybridDB for MySQL结果表 {#concept_63985_zh .concept}

本文为您介绍如何创建实时计算云数据库（HybridDB for MySQL）结果表。

## 什么是云数据库 HybridDB for MySQL {#section_dtx_4sm_cgb .section}

云数据库 HybridDB for MySQL（原名PetaData）是同时支持在线事务（OLTP）和在线分析（OLAP）的关系型 HTAP 类数据库。HTAP是Hybrid Transaction/Analytical Processing的简写，意为将数据的事务处理（TP）与分析（AP）混合处理，从而实现对数据的实时处理分析。

HybridDB for MySQL兼容MySQL的语法及函数，并且增加了对Oracle常用分析函数的支持。完全兼容TPC-H和TPC-DS测试标准。

## DDL定义 {#section_jvv_vsm_cgb .section}

实时计算支持使用HybridDB for MySQL作为结果输出。示例代码如下。

``` {#codeblock_6ep_zu5_l7d .language-sql}
create table petadata_output(
 id INT,
 len INT,
 content VARCHAR,
 primary key(id,len)
) with (
 type='petaData',
 url='yourDatabaseURL',
 tableName='yourTableName',
 userName='yourDatabaseUserName',
 password='yourDatabasePassword'
);
```

**说明：** 

-   实时计算写入PetaData数据库结果表原理：针对实时计算每行结果数据，拼接成一行SQL向目标端数据库进行执行。
-   bufferSize默认值是1000，如果到达bufferSize阈值的话（buffer hashmap size），也会触发写出。因此您配置batchSize的同时还需要配上bufferSize。bufferSize和batchSize大小相同即可。
-   建议设置batchSize='4096'，batchSize数值不建议设置过大。

## WITH参数 {#section_jkp_ysm_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|url|地址|[切换网络类型](../../../../cn.zh-CN/用户指南/管理实例/切换网络类型.md#)|
|tableName|表名|无|
|userName|用户名|无|
|password|密码|无|
|maxRetryTimes|最大重试次数|可选，默认为3。|
|batchSize|一次批量写入的条数|可选，默认值1000 ，表示每次写多少条。|
|bufferSize|流入多少条数据后开始去重|可选|
|flushIntervalMs|写超时时间|可选，单位为毫秒，默认值为3000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|ignoreDelete|是否忽略delete操作|默认为false|

