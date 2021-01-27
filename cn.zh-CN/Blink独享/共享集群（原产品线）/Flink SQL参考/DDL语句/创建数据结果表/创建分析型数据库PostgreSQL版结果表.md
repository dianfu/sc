# 创建分析型数据库PostgreSQL版结果表

本文为您介绍如何创建分析型数据库PostgreSQL版结果表，以及创建过程中涉及到的WITH参数和类型映射。

**说明：** 本文仅适用于Blink 3.6.0及以上版本。

## DDL定义

实时计算Flink版支持使用分析型数据库PostgreSQL版作为结果输出。示例代码如下。

```
create table rds_output(
  id INT,
  len INT,
  content VARCHAR,
  PRIMARY KEY(id)
) with (
  type='adbpg',
  url='jdbc:postgresql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
  tableName='<yourDatabaseTableName>',
  userName='<yourDatabaseUserName>',
  password='<yourDatabasePassword>'
);
```

## 实现原理

实时计算Flink版写入分析型数据库PostgreSQL结果表可以分为以下两个阶段：

1.  将实时计算Flink版每行的结果数据拼接为一行SQL。
2.  在目标数据库执行拼接后的SQL。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|源表类型|是|固定值为`adbpg`。|
|url|JDBC连接地址|是|分析型数据库PostgreSQL版数据库的JDBC连接地址。格式为'jdbc:postgresql://<yourNetworkAddress\>:<PortId\>/<yourDatabaseName\>'，其中：-   yourNetworkAddress：内网地址。
-   PortId：连接端口。
-   yourDatabaseName：连接的数据库名称。

示例：url='jdbc:postgresql://gp-xxxxxx.gpdb.cn-chengdu.rds.aliyuncs.com:3432/postgres'|
|tableName|表名|是|无|
|username|账号|是|无|
|password|密码|是|无|
|maxRetryTimes|写入重试次数|否|默认值为3。|
|useCopy|是否采用Copy API写入数据|否|参数取值如下： -   0（默认值）：采用INSERT方式写入数据。
-   1：采用copy API方式写入数据。 |
|batchSize|一次批量写入的条数|否|默认值为5000。|
|exceptionMode|数据写入过程中出现异常时的处理策略|否|支持以下两种处理策略： -   ignore（默认值）：忽略出现异常时写入的数据。
-   strict：数据写入异常时，Failover报错。 |
|conflictMode|当出现主键冲突或唯一索引冲突时的处理策略|否|支持以下三种处理策略： -   ignore（默认值）：忽略主键冲突，保留之前的数据。
-   strict：主键冲突时，Failover报错。
-   update：主键冲突时，更新新到的数据。 |
|targetSchema|Schema名称|否|默认值为public。|

## 类型映射

分析型数据库PostgreSQL和实时计算Flink版字段类型对应关系如下。

|分析型数据库PostgreSQL版字段类型|实时计算Flink版字段类型|
|---------------------|--------------|
|BOOLEAN|BOOLEAN|
|SMALLINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|DOUBLE PRECISION|DOUBLE|
|TEXT|VARCHAR|
|TIMESTAMP|DATETIME|
|DATE|DATE|
|REAL|FLOAT|
|DOUBLE PRECISION|DECIMAL|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|

