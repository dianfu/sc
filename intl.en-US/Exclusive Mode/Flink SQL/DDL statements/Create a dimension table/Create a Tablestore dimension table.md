# Create a Tablestore dimension table

This topic describes how to create a Tablestore dimension table in Realtime Compute for Apache Flink.

**Note:** This topic applies only to Blink 1.4.5 and later.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service that is built in the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts data sharding and load balancing technologies to scale out and handle concurrent transactions. You can use Tablestore to store and query a large amount of structured data in real time.

## Example

In Realtime Compute for Apache Flink, you can use a Tablestore table as a dimension table. The following code shows an example:

```
CREATE TABLE ots_dim_table (
 id int,
 len int,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) WITH (
 type='ots',
 endPoint='<yourEndpoint>'
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
);
```

**Note:**

-   When you declare a dimension table, you must specify a primary key.
-   When you join a dimension table with another dimension table, the ON condition must contain equivalent conditions that include all primary keys.
-   The primary key of Tablestore is the rowkey of the table.

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the dimension table.|Set the value to `ots`.|
|endPoint|The endpoint of a Tablestore instance.|If the instance is deployed in a VPC, enter the VPC endpoint of the instance.|
|instanceName|The name of the Tablestore instance.|None.|
|tableName|The name of the Tablestore table.|None.|
|accessId|The AccessKey ID read by Tablestore.|None.|
|accessKey|The AccessKey secret read by Tablestore.|None.|

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|Valid values for a Tablestore dimension table: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The cache size, in lines.|You can set this parameter when the cache parameter is set to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|You can set this parameter when the cache parameter is set to LRU.|

## Sample code

```
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

CREATE TABLE phoneNumber(
name VARCHAR,
phoneNumber bigint,
primary key(name),
PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
)with(
type='ots'
);

CREATE TABLE result_infor(
id bigint,
phoneNumber bigint,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id,
w.phoneNumber,
t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w -- You must include this clause in the INSERT INTO statement when you join a dimension table with another table.
ON t.name = w.name;     
```

For more information about the detailed syntax of a dimension table, see [JOIN \(for dimension tables\)](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/Dimension table JOIN statement.md).

