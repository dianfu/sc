# Create an AnalyticDB for PostgreSQL result table

This topic describes how to create an AnalyticDB for PostgreSQL result table. It also describes the parameters in the WITH clause and data type mapping used when you create such a result table.

**Note:** This topic applies only to Blink 3.6.0 and later.

## DDL syntax

In Realtime Compute for Apache Flink, you can use AnalyticDB for PostgreSQL to store output data. The following code shows an example:

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

## Implementation

Realtime Compute for Apache Flink writes data to an AnalyticDB for PostgreSQL result table in the following two steps:

1.  Converts each row of output data to a line of SQL statement.
2.  Writes and executes the SQL statement in the destination database.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to `adbpg`.|
|url|The Java Database Connectivity \(JDBC\) URL.|Yes|The JDBC URL used to access the AnalyticDB for PostgreSQL database. The format is 'jdbc:postgresql://<yourNetworkAddress\>:<PortId\>/<yourDatabaseName\>'.-   yourNetworkAddress: the internal IP address.
-   PortId: the port that is used to log on to the database.
-   yourDatabaseName: the name of the database.

Example: url='jdbc:postgresql://gp-xxxxxx.gpdb.cn-chengdu.rds.aliyuncs.com:3432/postgres'|
|tableName|The name of the table.|Yes|None.|
|username|The username that is used to log on to the database.|Yes|None.|
|password|The password that is used to log on to the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data.|No|Default value: 3.|
|useCopy|Specifies whether to use the copy API to write data.|No|Valid values: -   0 \(default value\): indicates that the INSERT statement is used to write data to the AnalyticDB for PostgreSQL database.
-   1: indicates that the copy API is used to write data to the AnalyticDB for PostgreSQL database. |
|batchSize|The number of data records that can be written at a time.|No|Default value: 5000.|
|exceptionMode|The policy that is used to handle exceptions during data writing.|No|Valid values: -   ignore \(default value\): Data that is written at the time an exception occurs is ignored.
-   strict: A failover is reported if an exception occurs during data writing. |
|conflictMode|The policy that is used to handle primary key conflicts or unique index conflicts.|No|Valid values: -   ignore \(default value\): Primary key conflicts are ignored and the existing data is retained.
-   strict: A failover is reported if a primary key conflict occurs.
-   update: The data is updated if a primary key conflict occurs. |
|targetSchema|The name of the schema.|No|Default value: public.|

## Field type mapping

The following table lists the mapping between the data types of AnalyticDB for PostgreSQL and Realtime Compute for Apache Flink.

|Data type of AnalyticDB for PostgreSQL|Data type of Realtime Compute for Apache Flink|
|--------------------------------------|----------------------------------------------|
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

