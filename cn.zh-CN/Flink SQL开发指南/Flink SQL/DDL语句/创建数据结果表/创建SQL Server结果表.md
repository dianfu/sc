# 创建SQL Server结果表 {#concept_186706 .concept}

本文为您介绍如何创建实时计算SQL Server结果表。

## DDL定义 {#section_mj7_1am_aye .section}

实时计算支持使用SQL Server作为结果输出。示例代码如下。

```language-sql
create table ss_output(
 id INT,
 len INT,
 content VARCHAR,
 primary key(id,len)
) with (
 type='jdbc',
 url='jdbc:sqlserver://ip:port;database=****',
 tableName='yourDatabaseTableName',
 userName='yourDatabaseUserName',
 password='yourDatabasePassword'
);
```

**说明：** 实时计算引用的SQL Server真实表需要定义主键，否则初始化阶段会产生NPE（NullPointerException）报错。

## WITH参数 {#section_xsf_nz7_f87 .section}

|参数|注释说明|备注|
|--|----|--|
|url|jdbc连接地址|地址请参见： -   [RDS的URL地址](https://help.aliyun.com/document_detail/26128.html?spm=5176.doc43185.6.581.rxQuNz)
-   [DRDS的URL地址](https://help.aliyun.com/document_detail/56494.html)

 |
|tableName|表名|无|
|username|账号|无|
|password|密码|无|
|maxRetryTimes|写入重试次数|可选，默认为3|
|bufferSize|流入多少条数据后开始去重|可选，默认为500，表示输入的数据达到500条就开始输出。|
|flushIntervalMs|清空缓存的时间间隔|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|excludeUpdateColumns|忽略指定字段的更新|可选，默认值为空（默认忽略primary key字段）。表示更新主键值相同的数据时，忽略指定字段的更新。|
|ignoreDelete|是否忽略delete操作|默认为false|

