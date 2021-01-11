---
keyword: [MongoDB, result table]
---

# Create an ApsaraDB for MongoDB result table

This topic describes how to create an ApsaraDB for MongoDB result table in Realtime Compute for Apache Flink. This parameter also describes the parameters in the WITH clause that is used when you create an ApsaraDB for MongoDB result table.

**Note:**

-   This topic applies to only Blink V3.2.2 and later.
-   You cannot update the primary key in an ApsaraDB for MongoDB result table. As a result, data that has the same primary key is inserted repeatedly into the table.

## Syntax

In Realtime Compute for Apache Flink, you can use ApsaraDB for MongoDB to store output data. To create an ApsaraDB for MongoDB result table, you can use the following sample code:

```
CREATE TABLE mongodb_sink (
  `a`             VARCHAR
) WITH (
   type = 'mongodb',
   database = '<yourDatabaseName>',
   collection= '<yourCollectionName>',
   uri='mongodb://{<databaseAccount>}:{<atabasePassword>}@{host}:****? replicaSet=mgset-1224****',
   keepAlive='true',
   maxConnectionIdleTime='20000',
   batchSize='2000'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the connector.|Yes|Set the value to mongodb.|
|database|The name of the ApsaraDB for MongoDB database.|Yes|None.|
|collection|The set of result table data.|Yes|None.|
|uri|The connection string of the ApsaraDB for MongoDB database.|Yes|None.|
|keepAlive|Specifies whether to maintain the persistent connection.|No|Default value: true.|
|maxConnectionIdleTime|The time-out duration of the connection.|No|The integer value. Unit: milliseconds. This parameter cannot be set to a negative value. Default value: 60000. If this parameter is set to 0, the connection does not time out.|
|batchSize|The number of data records that can be written at a time.|No|The integer value. Default value: 1024. The system sets the maximum number of data records that can be stored in the buffer. When the number of data records reaches the specified value of batchSize, the system triggers the data output. **Note:** When the checkpoint time arrives, the data output is triggered even if the number of data records in the buffer does not reach the specified value of batchSize. |

