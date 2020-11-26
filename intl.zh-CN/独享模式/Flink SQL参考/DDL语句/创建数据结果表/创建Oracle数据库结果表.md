---
keyword: [创建Oracle数据库结果表, Oracle数据库结果表写入注意事项]
---

# 创建Oracle数据库结果表

本文为您介绍如何创建Oracle结果表，以及创建结果表时使用的WITH参数、类型映射和注意事项。

**说明：**

-   本文仅适用于Blink 3.6.0及以上版本。
-   仅支持使用Oracle 19c版本创建Oracle数据库结果表。

## 注意事项

-   实时计算Flink版将每行结果数据拼接成一行SQL语句写入目标数据库。
-   根据数据库中是否存在需要的表，执行以下操作：
    -   当数据库中不存在需要的表时，Oracle结果表会新建一个用于写入结果的表。
    -   当数据库中存在需要的表时，Oracle结果表会向该表中写入或者更新数据。
-   逻辑表和物理表的主键不一定完全相同，要求逻辑表的主键包含物理表的主键。
-   使用以下两种函数处理Oracle数据库结果表的数据：
    -   如果没有定义主键，则执行Append函数向表中插入数据。
    -   如果已经定义主键，则执行Upsert函数向表中插入数据。当主键不存在时，则插入主键；当主键存在时，则更新主键。

## 语法示例

实时计算Flink版支持使用Oracle数据库作为结果表，代码示例如下。

```
CREATE TABLE oracle_sink(
  employee_id BIGINT,
  employee_name VARCHAR,
  employee_age INT,
  PRIMARY KEY(employee_id)
) WITH (
    type = 'oracle',
    url = '<yourUrl>',
    userName = '<yourUserName>',
    password = '<yourPassword>',
    tableName = '<yourTableName>'
);
```

## WITH 参数

|参数|描述|是否必选|示例值|
|--|--|----|---|
|type|结果表类型|是|固定值为oracle。|
|url|数据库连接串|是|`jdbc:oracle:thin:@192.168.171.62:1521:sit0`|
|userName|登录数据库的用户名|是|无|
|password|登录数据库的密码|是|无|
|tableName|数据库的表名|是|无|
|maxRetryTimes|向结果表插入数据的最大尝试次数|否|默认值为10。|
|batchSize|单次写入数据的批次大小 **说明：**

-   指定主键后batchSize参数才生效。
-   batchSize或bufferSize参数达到阈值时都会触发数据写入。

|否|默认值为50。|
|bufferSize|去重的缓存大小 **说明：**

-   指定主键后batchSize参数才生效。
-   batchSize或bufferSize参数达到阈值时都会触发数据写入。

|否|默认值为500。|
|flushIntervalMs|写超时时间，单位为毫秒。 **说明：** 如果在写超时时间内没有向数据库写入数据，系统会将缓存的数据再写一次。

|否|默认值为500。|
|excludeUpdateColumns|更新表的某行数据时，是否不更新该行指定的列数据。 **说明：** 更新表的某行数据时，默认不更新主键数据。

|否|默认不填写。|
|ignoreDelete|是否忽略删除操作。|否|默认值为false。|

## 类型映射

|Oracle 字段类型|实时计算Flink版字段类型|
|-----------|--------------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## 代码示例

实时计算Flink版包含Oracle数据库结果表的代码示例如下。

```
CREATE TABLE oracle_source (
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT
) WITH (
  type = 'random'
);

CREATE TABLE oracle_sink(
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT,
  primary key(employee_id)
)with(
  type = 'oracle',
  url = 'jdbc:oracle:thin:@192.168.171.62:1521:sit0',
  userName = 'blink_test',
  password = 'blink_test',
  tableName = 'oracle_sink'
);

INSERT INTO oracle_sink
SELECT * FROM oracle_source;
```

