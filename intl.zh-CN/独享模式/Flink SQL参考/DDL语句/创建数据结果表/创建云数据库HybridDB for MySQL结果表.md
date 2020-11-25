# 创建云数据库HybridDB for MySQL结果表

本文为您介绍如何创建实时计算Flink版云数据库HybridDB for MySQL结果表，以及创建结果表时使用的WITH参数。

**说明：**

-   阿里云数据库HybridDB for MySQL产品已下线。
-   本文仅适用于Blink 1.4.5及以上版本。

## 什么是云数据库HybridDB for MySQL

云数据库HybridDB for MySQL（原名PetaData）是同时支持海量数据在线事务（OLTP）和在线分析（OLAP）的HTAP（Hybrid Transaction/Analytical Processing）关系型数据库。HybridDB for MySQL采用一份数据存储来进行OLTP和OLAP处理，避免把一份数据复制多次进行数据分析，极大地降低了数据存储的成本。

## DDL定义

实时计算Flink版支持使用HybridDB for MySQL作为结果输出，示例代码如下。

```
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

-   实时计算Flink版写入PetaData数据库结果表原理：针对实时计算Flink版每行结果数据，拼接成一行SQL语句，输入至目标端数据库。
-   bufferSize默认值是1000，如果到达bufferSize阈值，则会触发写出。因此您配置batchSize的同时还需要配置bufferSize。bufferSize和batchSize大小相同即可。
-   batchSize数值不建议设置过大，建议设置batchSize='4096'。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为petaData。|
|url|地址|是|[切换网络类型](/intl.zh-CN/用户指南/管理实例/切换网络类型.md)。|
|tableName|表名|是|无|
|userName|用户名|是|无|
|password|密码|是|无|
|maxRetryTimes|最大重试次数|否|默认值为3。|
|batchSize|一次批量写入的条数|否|默认值为1000，表示每次写多少条。|
|bufferSize|流入多少条数据后开始去重|否|无|
|flushIntervalMs|写超时时间|否|单位为毫秒。默认值为3000，表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|ignoreDelete|是否忽略Delete操作|否|默认值为false。|

