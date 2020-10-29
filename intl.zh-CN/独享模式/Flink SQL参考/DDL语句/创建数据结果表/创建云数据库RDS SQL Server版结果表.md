---
keyword: [结果表, RDS]
---

# 创建云数据库RDS SQL Server版结果表

本文为您介绍如何创建云数据库RDS SQL Server版结果表，以及创建结果表时使用的WITH参数、类型映射和JDBC连接参数。

**说明：**

-   本文仅适用于Blink 3.2.0及以上版本。
-   实时计算Flink版暂不支持引用RDS SQL Server版本作为数据存储。

## 语法示例

实时计算Flink版支持使用云数据库RDS SQL Server版作为结果表输出，示例代码如下。

```
create table ss_output(
  id INT,
  len INT,
  content VARCHAR,
  primary key(id,len)
) with (
  type='jdbc',
  url='jdbc:sqlserver://ip:port;database=****',
  tableName='<yourDatabaseTableName>',
  userName='<yourDatabaseUserName>',
  password='<yourDatabasePassword>'
);
```

**说明：**

-   实时计算Flink版写入RDS和DRDS数据库结果表原理：针对实时计算Flink版每行结果数据，拼接成一行SQL语句，输入至目标端数据库。如果使用批量写，需要在URL后面加上参数?rewriteBatchedStatements=true，以提高系统性能。
-   RDS SQL Server数据库支持自增主键。如果需要让实时计算Flink版写入数据支持自增主键，则在DDL中不声明该自增字段。例如，ID是自增字段，实时计算Flink版DDL不写出该自增字段，则数据库在一行数据写入过程中会自动填补相关的自增字段。
-   如果DRDS有分区表，拆分键必须在实时计算Flink版DDL里primary key（）中声明，否则拆分的表无法写入。
-   DDL声明的字段必选至少存在一个非主键的字段，否则产生报错。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为jdbc。|
|url|JDBC（Java DataBase Connectivity）连接地址|是|地址请参见： -   [Apply for an Internet IP address for RDS](https://www.alibabacloud.com/help/doc-detail/26128.htm) |
|tableName|表名|是|无|
|username|账号|是|无|
|password|密码|是|无|
|maxRetryTimes|写入重试次数|否|默认值为10。|
|bufferSize|流入多少条数据后开始去重|否|默认值为10000，表示输入的数据达到10000条开始去重。|
|flushIntervalMs|清空缓存的时间间隔|否|单位为毫秒，默认值为2000，表示如果缓存中的数据在等待2秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|excludeUpdateColumns|忽略指定字段的更新|否|可选，默认值为空（默认忽略Primary Key字段），表示更新主键值相同的数据时，忽略指定字段的更新。|
|ignoreDelete|是否忽略Delete操作|否|默认值为False。|

## 类型映射

|RDS字段类型|实时计算Flink版字段类型|
|-------|--------------|
|BOOLEAN|BOOLEAN|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|VARBINARY|VARBINARY|

## JDBC连接参数

|参数|说明|默认值|最低版本要求|
|--|--|---|------|
|useUnicode|是否使用Unicode字符集。如果参数CharacterEncoding设置为GB2312或GBK，useUnicode值必须设置为True。|False|1.1g|
|characterEncoding|当useUnicode设置为True时，指定字符编码。例如可设置为GB2312或GBK。|False|1.1g|
|autoReconnect|当数据库连接异常中断时，是否自动重新连接。|False|1.1|
|autoReconnectForPools|是否使用针对数据库连接池的重连策略。|False|3.1.3|
|failOverReadOnly|自动重连成功后，连接是否设置为只读。|True|3.0.12|
|maxReconnects|autoReconnect设置为True时，重试连接的次数。|3|1.1|
|initialTimeout|autoReconnect设置为True时，两次重连之间的时间间隔，单位为秒。|2|1.1|
|connectTimeout|和数据库服务器建立socket连接时的超时，单位为毫秒。|0，表示永不超时，适用于JDK 1.4及以上版本。|3.0.1|
|socketTimeout|socket操作（读写）超时，单位为毫秒。|0，表示永不超时。|3.0.1|

## 代码示例

包含云数据库RDS SQL Server版结果表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE source (
  id INT,
  len INT,
  content VARCHAR
) with (
  type = 'random'
);

CREATE TABLE rds_output(
  id INT,
  len INT,
  content VARCHAR,
  PRIMARY KEY (id,len)
) WITH (
  type='jdbc',
  url='<yourDatabaseURL>',
  tableName='<yourDatabaseTable>',
  userName='<yourDatabaseUserName>',
  password='<yourDatabasePassword>'
);

INSERT INTO rds_output
SELECT id, len, content FROM source;
```

## 常见问题

-   Q：实时计算Flink版的结果数据写入RDS表，是按主键更新，还是生成1条新的记录？

    A：根据DDL中是否定义主键，分为以下两种方式：

    -   DDL中定义主键：采用`insert into on duplicate key update`的方式更新记录，即对于不存在的主键字段会直接插入，存在的主键字段则更新相应的值。
    -   DDL中没有定义主键：采用`insert into`的方式插入记录，追加数据。
-   Q：使用RDS表中的唯一索引进行GROUP BY操作需要注意什么？

    A：需要注意以下两点：

    -   在作业中的主键（Primary Key）中声明该唯一索引。
    -   RDS中只有一个自增主键，实时计算Flink版作业中不能将该自增主键声明为主键。

