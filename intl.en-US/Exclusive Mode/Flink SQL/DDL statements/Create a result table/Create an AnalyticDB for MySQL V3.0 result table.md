# Create an AnalyticDB for MySQL V3.0 result table

This topic describes how to create an AnalyticDB for MySQL V3.0 result table in Realtime Compute for Apache Flink. This topic also describes the parameters in the WITH clause that is used when you create the table.

**Note:**

-   You cannot register AnalyticDB for MySQL V3.0 storage resources in the Realtime Compute for Apache Flink console to store result tables.
-   This topic applies to only `Blink V3.3.0` and later.
-   For more information about how to create an AnalyticDB for MySQL V2.0 result table, see [Create an AnalyticDB for MySQL V2.0 result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an AnalyticDB for MySQL V2.0 result table.md).

## Syntax

In Realtime Compute for Apache Flink, you can use AnalyticDB for MySQL V3.0 to store output data. To create a MySQL V3.0 result table, you can use the following sample code:

```
CREATE TABLE rds_output (
id INT,
len INT,
content VARCHAR,
PRIMARY KEY(id,len)
) WITH (
type='ADB30',
url='jdbc:mysql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
tableName='<yourDatabaseTableName>',
userName='<yourDatabaseUserName>',
password='<yourDatabasePassword>'
);
```

## Implementation

Realtime Compute for Apache Flink writes output data to an AnalyticDB for MySQL V3.0 result table by performing the following two steps:

1.  In Realtime Compute for Apache Flink, an SQL statement is executed to write each row of result data to the result table in the AnalyticDB for MySQL V3.0 database.
2.  Realtime Compute for Apache Flink executes the SQL statement in the AnalyticDB for MySQL V3.0 database.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the connector.|Yes|Set the value to `ADB30`.|
|url|The Java Database Connectivity \(JDBC\) URL of the AnalyticDB for MySQL database.|Yes|The URL of the AnalyticDB for MySQL database, such as, `url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`. **Note:**

-   For more information about how to connect to an AnalyticDB for MySQL database, see [Query a URL](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an AnalyticDB for MySQL instance.md).
-   The databaseName parameter specifies the name of the AnalyticDB for MySQL database. |
|tableName|The name of the table.|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data to the table.|No|Default value: 3.|
|bufferSize|The maximum number of data records that can be stored in the buffer before duplicates are removed.|No|Default value: 1000. This value indicates that duplicates are removed when the number of input data records reaches 1,000. **Note:** This parameter is valid only after you specify the primary key. |
|batchSize|The number of data records that can be written at a time.|No|Default value: 1000. **Note:** This parameter is valid only after you specify the primary key. |
|flushIntervalMs|The time interval at which the cache is cleared.|No|Default value: 3000. Unit: milliseconds. This value indicates that all the cached data is written to the result table if the number of input data records does not reach the batchSize value within 3,000 milliseconds.|
|ignoreDelete|Specifies whether to ignore delete operations.|No|Default value: false. This value indicates that the delete operations are supported.|
|reserveMilliSecond|Specifies whether to reserve the millisecond component in a value of the TIMESTAMP data type.|No|Default value: false. This value indicates that the millisecond component is not reserved.|

