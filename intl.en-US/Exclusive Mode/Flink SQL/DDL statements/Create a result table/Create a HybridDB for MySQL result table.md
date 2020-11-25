# Create a HybridDB for MySQL result table

This topic describes how to create a HybridDB for MySQL result table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause used when you create such a result table.

**Note:**

-   HybridDB for MySQL is no longer available.
-   This topic applies only to Blink 1.4.5 and later.

## Introduction to HybridDB for MySQL

HybridDB for MySQL \(formerly known as PetaData\) is a hybrid transaction/analytical processing \(HTAP\) relational database that supports both online transaction processing \(OLTP\) and online analytical processing \(OLAP\). HybridDB for MySQL uses the same data store for OLTP and OLAP. This prevents data replications during data analysis and significantly reduces data storage costs.

## DDL syntax

In Realtime Compute for Apache Flink, you can use HybridDB for MySQL to store output data. The following code shows an example:

```
create table petadata_output(
 id INT,
 len INT,
 content VARCHAR,
 primary key(id,len)
) with (
 type='petaData',
 url='yourDatabaseURL',
 tableName='yourTableName',
 userName='yourDatabaseUserName',
 password='yourDatabasePassword'
);
```

**Note:**

-   In Realtime Compute for Apache Flink, each row of output data is converted to a line of SQL statement and then written and executed in the destination database.
-   The default value of the bufferSize parameter is 1000. If the number of data records reaches the value of this parameter, data is written into the result table. If you specify the batchSize parameter, you must also specify the bufferSize parameter. You can set the two parameters to the same value.
-   We recommend that you set the batchSize parameter to 4096. Do not set it to a large value.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to petaData.|
|url|The URL that is used to access the database.|Yes|[Switch network type](/intl.en-US/User Guide/Manage instances/Switch network type.md).|
|tableName|The name of the table.|Yes|None.|
|userName|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data to the table.|No|Default value: 3.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 1000.|
|bufferSize|The maximum number of data records that can be stored in the buffer before deduplication is triggered.|No|None.|
|flushIntervalMs|The timeout period for writing data.|No|Unit: milliseconds. Default value: 3000. This value indicates that if the number of input data records does not reach the value specified by the bufferSize parameter within 3,000 milliseconds, all cached data is written into the result table.|
|ignoreDelete|Specifies whether to skip delete operations.|No|Default value: false.|

