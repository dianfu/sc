---
keyword: 创建Oracle数据库结果表
---

# Oracle结果表

本文为您介绍Oracle结果表的DDL定义，以及创建结果表时使用的WITH参数、类型映射和代码示例。

## 注意事项

-   Flink将每行结果数据拼接成一行SQL语句写入目标数据库进行执行。
-   根据数据库中是否存在需要的表，执行以下操作：
    -   当数据库中存在需要的表时，Oracle结果表会向该表中写入或者更新数据。
    -   当数据库中不存在需要的表时，Oracle结果表会新建一个用于写入结果的表。
-   逻辑表和物理表的主键不一定完全相同，要求逻辑表的主键包含物理表的主键，否则在插入结果数据时会报主键唯一性的错误。
-   使用以下两种函数处理Oracle数据库结果表的数据：
    -   如果没有定义主键，则执行Append函数向表中插入数据。
    -   如果已经定义主键，则执行Upsert函数向表中插入数据。当主键不存在时，则插入主键；当主键存在时，则更新主键。

## DDL定义

```
CREATE TABLE oracle_sink (
  employee_id BIGINT,
  phone_number VARCHAR,
  employee_age INT,
  PRIMARY KEY(employee_id)
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);
```

## WITH参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为oracle。|
|url|JDBC的Oracle地址|是|`jdbc:oracle:thin:@192.168.171.62:1521:sit0`|
|userName|数据库的用户名|是|无|
|password|数据库的密码|是|无|
|tableName|表名称|是|无|
|maxRetryTimes|向结果表插入数据的最大尝试次数|否|默认值为10。|
|batchSize|一次批量写入的条数。**说明：**

-   指定主键后batchSize参数才生效。
-   batchSize或bufferSize参数达到阈值时都会触发数据写入。

|否|默认值为50。|
|bufferSize|流入多少条数据后开始去重。**说明：**

-   指定主键后batchSize参数才生效。
-   batchSize或bufferSize参数达到阈值时都会触发数据写入。

|否|默认值为500。|
|flushIntervalMs|写超时时间，单位为毫秒。 **说明：** 如果在写超时时间内没有向数据库写入数据，系统会将缓存的数据再写一次。

|否|默认值为500。|
|excludeUpdateColumns|忽略指定字段的更新。|否|默认为空，表示更新表的某行数据时，不更新主键数据。|
|ignoreDelete|是否忽略删除操作的数据。|否|取值如下：-   false（默认值）：不忽略。
-   true：忽略。 |

## 类型映射

|Oracle字段类型|Flink字段类型|
|----------|---------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## 代码示例

```
CREATE TEMPORARY TABLE oracle_source (
  employee_id BIGINT,
  employee_name VARCHAR,
  employee_age INT
) WITH (
  'connector'='datagen'
);

CREATE TEMPORARY TABLE oracle_sink (
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT,
  primary key(employee_id)
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);

INSERT INTO oracle_sink SELECT * FROM oracle_source;
```

