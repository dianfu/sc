---
keyword: [创建Oracle数据库源表, Oracle数据库字段, 查询Oracle源表数据失败]
---

# 创建Oracle数据库源表

本文为您介绍如何创建Oracle数据库源表，以及创建源表时使用的WITH参数、类型映射、代码示例和常见问题。

**说明：**

-   本文仅适用于Blink 3.4.x及以上版本。
-   仅支持使用Oracle 11g版本创建Oracle数据库源表。
-   请不要手动修改Oracle Source节点的并发数量，默认一个Table对应一个并发。

## 语法示例

实时计算Flink版支持使用Oracle数据库作为源表，代码示例如下。

```
create table oracle_source (
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type = 'oracle',
    url = 'jdbc:oracle:thin:@//127.0.0.1:1521/ORACLE',
    userName = 'userName',
    password = 'password',
    dbName = 'hr',
    tableName = 'job_history',
    timeField = 'START_DATE',
    startTime = '2007-1-1 00:00:00'
);
```

## WITH 参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|type|源表类型|是|固定值为oracle。|
|url|数据库连接串|是|固定句式为`jdbc:oracle:thin:@//数据库IP:端口号/数据库名`。例如jdbc:oracle:thin:@//127.0.0.1:1521/XE。|
|userName|登录数据库的用户名|是|无|
|password|登录数据库的密码|是|无|
|tableName|数据库的表名。数据库的表名有以下两种表达方式： -   表名1,表名2
-   数据库名.表名1,表名2

 **说明：** 多个表名之间用英文逗号（,）隔开。

|是|-   table1,table2
-   db1.table1,table2 |
|timeField|更新数据库的时间|是|无|
|dbName|数据库名|否|如果tableName参数配置了数据库名，则dbName不需要重复配置。|
|startTime|读取数据的开始时间|否|2019-5-15 00:00:00|
|timeZone|数据库时区|否|Asia/Shanghai", "UTC|
|queryTimeRangeMs|获取数据的时长，单位为毫秒。 **说明：** queryTimeRangeMs参数的取值需要大于queryIntervalMs参数。

|否|默认值为5000。|
|queryIntervalMs|查询数据库的时间间隔，单位为毫秒。|否|默认值为100。|
|connectionMaxActive|最大活跃连接数|否|默认值为10。|
|maxRetry|最大连接失败重试次数|否|默认值为3。|
|escapeFields|是否对数据库字段名进行转义。|否|escapeFields参数的取值如下： -   false（默认值），不区分大小写。
-   true，区分大小写。 |
|lengthCheck|单行字段条数的检查策略|否|lengthCheck参数的取值如下： -   NONE（默认值）：
    -   当解析的字段个数大于定义个数时，按从左到右的顺序，取定义字段的数据使用。
    -   当解析的字段个数小于定义个数时，跳过当前行数据。
-   SKIP：当解析的字段个数和定义个数不同时，跳过当前行数据。
-   EXCEPTION：当解析的字段个数和定义个数不同时，系统提示异常。
-   PAD：
    -   当解析的字段个数大于定义个数时，按从左到右的顺序，取定义字段的数据使用。
    -   当解析的字段个数小于定义个数时，按从左到右顺序，在行尾用null填充缺少的字段。 |
|columnErrorDebug|是否打开调试开关。 **说明：** 如果打开调试开关，则会将解析异常的日志打印出来。

|否|默认值为false。|

## 类型映射

|Oracle字段类型|实时计算Flink版字段类型|
|----------|--------------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## 代码示例

实时计算Flink版包含Oracle数据库源表的代码示例如下。

```
create table oracle_source (
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type = 'oracle',
    url = 'jdbc:oracle:thin:@//127.0.0.1:1521/ORACLE',
    userName = 'userName',
    password = 'password',
    dbName = 'hr',
    tableName = 'job_history',
    timeField = 'START_DATE',
    startTime = '2007-1-1 00:00:00'
);

create table test_out(
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type='print'
);

INSERT INTO test_out
SELECT 
    EMPLOYEE_ID,
    START_DATE,
    END_DATE,
    JOB_ID,
    DEPARTMENT_ID
from oracle_source;
```

## 常见问题

Q：查询不到数据该如何处理？

A： 查询不到数据是因为Blink运行故障。您需要查看TaskManager的`Round start:[{}], end:[{}]`和`Round read records`日志，如果未查询到日志数据，则Blink运行故障。

**说明：**

-   `Round start:[{}], end:[{}]`用来显示查询数据的起始时间戳。
-   `Round read records`用来显示查询到的数据记录。

