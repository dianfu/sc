---
keyword: [result table, RDS]
---

# Create an ApsaraDB RDS for SQL Server result table

This topic describes how to create an ApsaraDB RDS for SQL Server result table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause, data type mapping, and JDBC parameters used when you create such a result table.

**Note:**

-   This topic applies only to Blink 3.2.0 and later.
-   Realtime Compute for Apache Flink does not use ApsaraDB RDS for SQL Server as a data store.

## Syntax

In Realtime Compute for Apache Flink, you can use ApsaraDB RDS for SQL Server to store output data. The following code shows an example:

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

**Note:**

-   In Realtime Compute for Apache Flink, each row of output data is converted to a line of SQL statement and then written and executed in the destination database. If you want to write multiple rows of output data at a time, you must add ? rewriteBatchedStatements=true to the end of the URL. This improves system efficiency.
-   You can define an auto-increment primary key for an ApsaraDB RDS for SQL Server database. If you want to use the auto-increment primary key, do not declare the auto-increment field in the DDL statement. For example, if you use the ID field as an auto-increment field, do not declare the ID field in the DDL statement. When a row of output data is written into an ApsaraDB RDS for SQL Server database, a value is automatically filled for the auto-increment field.
-   If a DRDS result table has partitions, the shard key must be declared in primary key\(\) of the DDL statement. Otherwise, you cannot write data into the partitioned table.
-   The fields declared in a DDL statement must include at least one non-primary key field. Otherwise, an error is returned.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to jdbc.|
|url|The Java Database Connectivity \(JDBC\) URL of the database.|Yes|For more information, see: -   [Apply for an Internet IP address for RDS](https://www.alibabacloud.com/help/doc-detail/26128.htm) |
|tableName|The name of the table.|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data into the table.|No|Default value: 10.|
|bufferSize|The maximum number of data records that can be stored in the buffer before deduplication is triggered.|No|Default value: 10000. This value indicates that deduplication is triggered after the number of input data records reaches 10,000.|
|flushIntervalMs|The time interval at which the buffer is cleared.|No|Unit: milliseconds. Default value: 2000. This value indicates that if the number of input data records does not reach the value specified by the bufferSize parameter within 2,000 milliseconds, all cached data is written into the result table.|
|excludeUpdateColumns|Specifies whether to ignore the update of a specific field.|No|This parameter is optional. It is empty by default, which indicates that the primary key field is ignored by default. When data with the same primary key is updated, the specified columns are not updated.|
|ignoreDelete|Specifies whether to skip delete operations.|No|Default value: False.|

## Data type mapping

|Data type of ApsaraDB RDS for SQL Server|Data type of Realtime Compute for Apache Flink|
|----------------------------------------|----------------------------------------------|
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

## JDBC parameters

|Parameter|Description|Default value|Required JDBC version|
|---------|-----------|-------------|---------------------|
|useUnicode|Specifies whether to use the Unicode character set. This parameter must be set to True if you set the characterEncoding parameter to GB2312 or GBK.|False|1.1g|
|characterEncoding|The character encoding format, such as GB2312 or GBK. If useUnicode is set to true, you must specify a character encoding format.|False|1.1g|
|autoReconnect|Specifies whether to automatically re-establish a connection if the connection to the database is unexpectedly interrupted.|False|1.1|
|autoReconnectForPools|Specifies whether to apply the reconnection policy to a database connection pool.|False|3.1.3|
|failOverReadOnly|Specifies whether to set the connection to read-only after the database is automatically reconnected.|True|3.0.12|
|maxReconnects|The maximum number of reconnection attempts allowed. This parameter must be set if the autoReconnect parameter is set to True.|3|1.1|
|initialTimeout|The interval between two reconnection attempts. Unit: seconds. This parameter must be set if the autoReconnect parameter is set to True.|2|1.1|
|connectTimeout|The timeout period when you use a socket connection to access the database server. Unit: milliseconds.|Default value: 0. This value indicates that the connection never times out. This parameter is provided in JDK V1.4 and later.|3.0.1|
|socketTimeout|The timeout period for read and write operations on a socket connection. Unit: milliseconds.|The value 0 indicates that the read or write operation never times out.|3.0.1|

## Sample code

The following example demonstrates how to create an ApsaraDB RDS for SQL Server result table in a Realtime Compute for Apache Flink job.

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

## FAQ

-   Q: When the output data of Realtime Compute for Apache Flink is written into an ApsaraDB RDS for SQL Server table, is the result table updated based on the primary key or is a new data record generated in the table?

    A: The processing method depends on whether the primary key is defined in the DDL statement.

    -   If a primary key is defined in the DDL statement, the result table is updated by using `insert into on duplicate key update`. For a data record, if the primary key does not exist, the record is inserted into the table as a new row. If the value of the primary key field exists, the original row in the table is updated.
    -   If no primary key is defined in the DDL statement, new data records are appended to the table by using `insert into`.
-   Q: What do I need to pay attention to when I perform GROUP BY operations based on the unique index of an ApsaraDB RDS for SQL Server table?

    A: Pay attention to the following items:

    -   Declare the unique index in the primary key of your job.
    -   An ApsaraDB RDS for SQL Server table has only one auto-increment primary key. Therefore, this auto-increment primary key cannot be declared as the primary key in a Realtime Compute for Apache Flink job.

