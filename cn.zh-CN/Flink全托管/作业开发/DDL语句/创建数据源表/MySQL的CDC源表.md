---
keyword: [MySQL的CDC, CDC]
---

# MySQL的CDC源表

本文为您介绍MySQL的CDC（Change Data Capture）源表DDL定义、WITH参数、类型映射和代码示例。

## 什么是MySQL的CDC源表

MySQL的CDC源表，即MySQL的流式源表，用于依次读取MySQL数据库全量快照数据和变更数据，保证不多读一条也不少读一条数据。即使发生故障，也能采用Exactly Once方式处理。

**说明：** MySQL的CDC源表需要一个有特定权限（包括SELECT、RELOAD、SHOW DATABASES、REPLICATION SLAVE和REPLICATION CLIENT）的MySQL用户，才能读取全量和增量数据。Flink全托管的MySQL CDC connector支持读取的MySQL版本为5.7和8.0X。

## DDL定义

```
CREATE TABLE orders (
  order_id INT,
  order_date TIMESTAMP(0),
  customer_name STRING,
  price DECIMAL(10, 5),
  product_id INT,
  order_status BOOLEAN
) WITH (
  'connector' = 'mysql-cdc',
  'hostname' = 'localhost',
  'port' = '3306',
  'username' = 'root',
  'password' = '123456',
  'database-name' = 'mydb',
  'table-name' = 'orders'
);
```

## WITH参数

|参数|说明|是否必填|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型|是|STRING|固定值为`mysql-cdc`。|
|hostname|MySQL数据库的IP地址或者Hostname|是|STRING|无|
|username|MySQL数据库服务的用户名|是|STRING|无|
|password|MySQL数据库服务的密码|是|STRING|无|
|database-name|MySQL数据库名称|是|STRING|数据库名称支持正则表达式以读取多个数据库的数据。|
|table-name|MySQL表名|是|STRING|表名支持正则表达式以读取多个表的数据。|
|port|MySQL数据库服务的端口号|否|INTEGER|默认值为3306。|
|server-id|数据库客户端的一个ID|否|STRING|该ID必须是MySQL集群中全局唯一的。建议针对同一个数据库的每个作业都设置一个不同的ID。默认会随机生成一个5400 ~ 6400 的值。|
|server-time-zone|数据库在使用的会话时区|否|STRING|例如Asia/Shanghai，该参数控制了MySQL中的TIMESTAMP类型如何转成STRING类型。|
|debezium.\*|Debezium属性参数|否|STRING|从更细粒度控制Debezium客户端的行为。例如`'debezium.snapshot.mode' = 'never'`，详情请参见[配置属性](https://debezium.io/documentation/reference/1.2/connectors/mysql.html#mysql-connector-configuration-properties_debezium)。|

## 类型映射

分析型数据库PostgreSQL和实时计算Flink版字段类型对应关系如下。

|MySQL CDC字段类型|实时计算Flink版字段类型|
|-------------|--------------|
|TINYINT|TINYINT|
|SAMLLINT|SMALLINT|
|TINYINT UNSIGNED|
|INT|INT|
|MEDIUMINT|
|SMALLINT UNSIGNED|
|BIGINT|BIGINT|
|INT UNSIGNED|
|BIGINT UNSIGNED|DECIMAL\(20, 0\)|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|DOUBLE PRECISION|
|NUMERIC\(p, s\)|DECIMAL\(p, s\)|
|DECIMAL\(p, s\)|
|BOOLEAN|BOOLEAN|
|TINYINT\(1\)|
|DATE|DATE|
|TIME \[\(p\)\]|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|
|DATETIME \[\(p\)\]|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|
|TIMESTAMP \[\(p\)\]|TIMESTAMP \[\(p\)\]|
|TIMESTAMP \[\(p\)\] WITH LOCAL TIME ZONE|
|CHAR\(n\)|STRING|
|VARCHAR\(n\)|
|TEXT|
|BINARY|BYTES|
|VARBINARY|
|BLOB|

## 常见问题

-   Q：如何跳过Snapshot阶段，只从变更数据开始读取？

    A：可以通过WITH参数debezium.snapshot.mode来控制，您可以设置为：

    -   never：在启动时，不读取数据库的快照，而是直接从变更的最开始位置读取。但需要注意mysql的变更旧数据可能会被自动清理，因此不能保证变更数据中包含了全量的数据，读取的数据不完整。
    -   schema\_only：如果你不需要保证数据的一致性，只关心作业启动后数据库的新增变更，则可以设置为schema\_only，仅快照schema，不快照数据，从变更的最新数据开始读取。
-   Q：如何读取一个分库分表的MySQL数据库？

    如果MySQL是一个分库分表的数据库，分成了user\_00、user\_02和user\_99等多个表，且所有表的schema一致。则可以通过table-name选项，指定一个正则表达式来匹配读取多张表，例如设置table-name为user\_.\*，监控所有user\_前缀的表。database-name选项也支持该功能，但需要所有的表schema一致。


