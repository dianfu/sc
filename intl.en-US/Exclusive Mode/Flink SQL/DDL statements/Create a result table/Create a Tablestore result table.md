# Create a Tablestore result table

This topic describes how to create a Tablestore result table in Realtime Compute for Apache Flink. It also describes the mappings between the field data types of Tablestore and Realtime Compute for Apache Flink.

**Note:** This topic applies only to Blink 1.4.5 and later.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service built on the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts data sharding and load balancing technologies to scale out and handle concurrent transactions. You can use Tablestore to store and query a large amount of structured data in real time.

## DDL syntax

In Realtime Compute for Apache Flink, you can use Tablestore to store output data. The following code shows an example:

```
CREATE TABLE stream_test_hotline_agent (
 name VARCHAR,
 age BIGINT,
 birthday BIGINT,
 PRIMARY KEY (name,age)
) WITH (
 type='ots',
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessSecret>',
 endPoint='<yourEndpoint>',
 valueColumns='birthday'
); 
```

**Note:**

-   We recommend that you use the storage registration feature. For more information, see [Register a Tablestore instance](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register a Tablestore instance.md).
-   The value of the valueColumns parameter cannot be a declared primary key.
-   The declared Tablestore result table must contain at least one attribute column and the primary key column.

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the result table.|Set the value to ots.|
|instanceName|The name of a Tablestore instance.|None.|
|tableName|The name of the table in the database|None.|
|endPoint|The endpoint of the instance.|For more information, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).|
|accessId|AccessKey ID|None.|
|accessKey|AccessKey Secret|None.|
|valueColumns|The name of a column to be inserted.|Separate multiple column names with commas \(,\), for example, `'ID,NAME'`.|
|bufferSize|The maximum number of data records that can be stored in the buffer before deduplication is triggered.|Optional. Default value: 5000. This value indicates that deduplication is triggered if the number of input data records in the buffer reaches 5,000. **Note:** Realtime Compute for Apache Flink removes data record duplicates based on the primary key of the Tablestore result table. You can set bufferSize to the number of data record duplicates to be removed. Then, Realtime Compute for Apache Flink writes the data records after duplicates are removed. You can set batchSize to the number of data records to be written at a time. |
|batchWriteTimeoutMs|The write timeout period.|Optional. Default value: 5000. Unit: milliseconds. This value indicates that if the number of input data records does not reach the value specified by the batchSize parameter within 5,000 milliseconds, all cached data is written into the result table.|
|batchSize|The number of data records that can be written at a time.|Optional. Default value: 100.|
|retryIntervalMs|The retry interval.|Optional. Default value: 1000. Unit: milliseconds.|
|maxRetryTimes|The maximum number of retries for writing data to a table.|Optional. Default value: 100.|
|ignoreDelete|Specifies whether to ignore DELETE operations.|Default value: false.|

## Field type mapping

|Data type of Tablestore|Data type of Realtime Compute for Apache Flink|
|-----------------------|----------------------------------------------|
|INTEGER|BIGINT|
|STRING|VARCHAR|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE|

**Note:** You must define a `primary key` in a Tablestore result table. Output data is appended to the Tablestore result table to update the result. For more information about update methods, see [Update type](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Overview of result tables.md).

