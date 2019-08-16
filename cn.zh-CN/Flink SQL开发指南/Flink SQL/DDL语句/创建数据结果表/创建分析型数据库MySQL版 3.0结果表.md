# 创建分析型数据库MySQL版 3.0结果表 {#concept_1715680 .concept}

本文为您介绍如何创建实时计算分析型数据库MySQL版3.0版本的结果表。

**说明：** 

-   本文档仅适用于`blink3.3.0`及以上版本。
-   AnalyticDB for MySQL 2.0版本结果表创建说明参见[创建分析型数据库MySQL版结果表](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据结果表/创建分析型数据库MySQL版结果表.md#)。

## DDL定义 {#section_0hq_2n4_y45 .section}

实时计算支持使用分析型数据库MySQL版3.0版本（AnalyticDB for MySQL 3.0）作为结果输出。示例代码如下。

``` {#codeblock_4kv_s47_99f .language-sql}
CREATE TABLE rds_output (
id INT,
len INT,
content VARCHAR，
PRIMARY KEY(id,len)
) WITH (
type='ADB30',
url='jdbc:mysql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
tableName='<yourDatabaseTableName>',
userName='<yourDatabaseUserName>',
password='<yourDatabasePassword>'
);
```

**说明：** 实时计算写入AnalyticDB for MySQL 3.0结果表的原理：

1.  将流式计算每行的结果数据拼接为一行SQL。
2.  将拼接后的SQL在目标数据库执行。

## WITH参数 {#section_n6p_pbj_sko .section}

|参数|注释说明|是否必选|备注|
|--|----|----|--|
|type|类型|是|固定值，为`ADB30`。|
|url|jdbc连接地址|是|分析型数据库MySQL版数据库地址 。示例：`url ='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`。 **说明：** 

-   分析型数据库MySQL版数据库连接信息参见[注册分析型数据库（AnalyticDB）](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册分析型数据库MySQL版.md#)中**URL地址查询**。
-   AnalyticDB数据库名称（databaseName）即AnalyticDB实例名称。

 |
|tableName|表名|是|无。|
|username|账号|是|无。|
|password|密码|是|无。|
|maxRetryTimes|写入重试次数|否|默认为3。|
|bufferSize|流入多少条数据后开始去重|否|默认为1000，表示输入的数据达到1000条则开始输出。 **说明：** 需要指定主键后才能生效。

 |
|batchSize|一次批量写入的条数|否|默认值为1000。 **说明：** 需要指定主键后才能生效。

 |
|flushIntervalMs|清空缓存的时间间隔|否|单位为毫秒，默认值为3000。表示如果缓存中的数据在等待3秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|ignoreDelete|是否忽略delete操作|否|默认为false，表示支持delete功能。|
|replaceMode|是否采用replace语法插入|否|默认为true，即采用Replace Into的语法插入。可选参数值如下： -   true：采用Replace Into的语法插入。
-   false采用Insert Into On Duplicate Key语法进行插入。

 |
|reserveMilliSecond|TimeStamp类型是否保留毫秒|否|默认false，不保留毫秒数值。|

