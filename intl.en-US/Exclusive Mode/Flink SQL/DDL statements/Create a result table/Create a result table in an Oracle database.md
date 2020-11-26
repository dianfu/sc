---
keyword: [create a result table in an Oracle database, considerations for writing data to an Oracle database result table]
---

# Create a result table in an Oracle database

This topic describes how to create a result table in an Oracle database. This topic also describes the parameters in the WITH clause, the mappings between the field data types of Oracle and Realtime Compute for Apache Flink, and the considerations. You may need to use these items when you create the table.

**Note:**

-   This topic applies to only Blink V3.6.0 and later.
-   You can create result tables in an Oracle database only when you use Oracle 19c.

## Notes

-   In Realtime Compute for Apache Flink, an SQL statement is executed to write each row of result data to the result table in the destination database.
-   Realtime Compute for Apache Flink performs the following operations based on whether the required table exists in the Oracle database:
    -   If the required table does not exist, the underlying layer of Blink creates a table in the Oracle database to store the result data.
    -   If the required table exists, Realtime Compute for Apache Flink writes or updates data to the result table.
-   The primary keys of logical and physical tables can be different. The primary keys of logical tables must include the primary keys of physical tables.
-   Realtime Compute for Apache Flink uses the APPEND function or the UPSERT function to insert data into an Oracle database result table.
    -   If the primary key of the table is not defined, use the APPEND function.
    -   If the primary key is defined, use the UPSERT function. If the primary key does not exist, insert the primary key. If the primary key exists, update the primary key.

## Syntax

In Realtime Compute for Apache Flink, you can use an Oracle database to store output data. To use an Oracle database to store a result table, you can use the following sample code:

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

## Parameters in the WITH clause

|Parameter|Description|Required|Example|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to oracle.|
|url|The connection string of the database.|Yes|`jdbc:oracle:thin:@192.168.171.62:1521:sit0`|
|userName|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table in the database.|Yes|None.|
|maxRetryTimes|The maximum number of attempts to insert data into the result table.|No|Default value: 10.|
|batchSize|The number of data records that are written at a time. **Note:**

-   The batchSize parameter takes effect only after the primary key is defined.
-   Write operations are triggered when the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 50.|
|bufferSize|The number of data records in the buffer after duplicates are removed. **Note:**

-   The batchSize parameter takes effect only after the primary key is defined.
-   Write operations are triggered when the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 500.|
|flushIntervalMs|The time-out period of the write operation. Unit: milliseconds. **Note:** If no data is written to the database within the period that is specified by this parameter, the system writes the cached data to the result table again.

|No|Default value: 500.|
|excludeUpdateColumns|Specifies whether to skip the specified columns when the data of a row in the table is updated. **Note:** By default, the primary key is not updated when the data of a row in the table is updated.

|No|By default, this parameter is empty.|
|ignoreDelete|Specifies whether to ignore delete operations.|No|Default value: false.|

## Field type mappings

|Oracle field type|Realtime Compute for Apache Flink field type|
|-----------------|--------------------------------------------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## Sample code

For a Realtime Compute for Apache Flink job, you can use the following sample code to create a result table in an Oracle database.

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

