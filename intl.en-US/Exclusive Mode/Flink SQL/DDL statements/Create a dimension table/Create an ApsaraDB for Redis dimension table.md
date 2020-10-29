---
keyword: [Redis, dimension table]
---

# Create an ApsaraDB for Redis dimension table

This topic describes how to create an ApsaraDB for Redis dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses, data type mapping, and sample code used when you create such a dimension table.

**Note:**

-   This topic applies only to Blink 3.2.2 and later.
-   ApsaraDB for Redis dimension tables in Realtime Compute for Apache Flink can only reference data of the STRING type in ApsaraDB for Redis databases.
-   ApsaraDB for Redis dimension tables in Realtime Compute for Apache Flink support user-created Redis databases.

## Syntax

In Realtime Compute for Apache Flink, you can create an ApsaraDB for Redis dimension table. The following code shows an example:

```
CREATE TABLE white_list (
  id VARCHAR,
  name VARCHAR,
  PRIMARY KEY (id),  -- The Row Key field in an ApsaraDB for Redis database.
  PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) WITH (
  type = 'redis',
  host = '<yourHostName>',
  port = '<yourPort>',
  password = '<yourPassword>',
  dbNum = '<yourDatabaseNumber>'
);
```

**Note:**

-   Only one primary key can be declared for an ApsaraDB for Redis dimension table.
-   When you join a dimension table with another dimension table, the ON condition must contain equivalent conditions of all the primary keys.
-   You can declare only two fields for an ApsaraDB for Redis dimension table, and the fields must be of the VARCHAR type.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the dimension table.|Yes|Set the value to `redis`.|
|host|The endpoint of an ApsaraDB for Redis instance.|Yes|None.|
|port|The port of the ApsaraDB for Redis database.|No|Default value: 6379.|
|dbNum|The sequence number of the ApsaraDB for Redis database.|No|Default value: 0.|
|password|The password that is used to access the ApsaraDB for Redis database.|No|This parameter is empty by default, which indicates that permission verification is not required.|
|hashName|The hash key name in hash mode.|No|This parameter is empty by default, which indicates that Realtime Compute for Apache Flink reads data of the STRING type from the ApsaraDB for Redis database. In typical cases, the data type in the Redis dimension table is STRING, which is represented as `key-value` pairs. If you set the hashName parameter, data in the ApsaraDB for Redis dimension table is of the HASHMAP type, which is presented as `key-{field-value}` pairs.

-   key is the value of the hashName parameter.
-   field is the value of the key parameter that you specify in the CREATE TABLE statement.
-   value is the value assigned to key, which has the same meaning as value in `key-value` of the STRING type. |

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The policy for caching data.|No|Valid values: -   None \(default value\): indicates that no data is cached.
-   LRU: indicates that partial data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If this cache policy is used, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of data records that can be cached. Unit: lines.|No|If you set the cache parameter to `LRU`, you can set this parameter to specify the maximum number of data records that can be cached. Default value: 10000.|
|cacheTTLMs|The cache timeout period.|No|The cache does not expire by default. Unit: milliseconds. If the cache policy is set to LRU, this parameter specifies the time before the cache expires.|
|cacheEmpty|Specifies whether to clear the cache.|No|Default value: true.|

## Field type mapping

The following table describes the mapping between ApsaraDB for Redis data types and data types of Realtime Compute for Apache Flink. We recommend that you declare the mapping in a DDL statement.

|Data type of ApsaraDB for Redis|Data type of Realtime Compute for Apache Flink|
|-------------------------------|----------------------------------------------|
|STRING|VARCHAR|

## Sample code

The following sample code demonstrates how to create an ApsaraDB for Redis dimension table in a Realtime Compute for Apache Flink job.

```
CREATE TABLE event (
  id VARCHAR, 
  data VARCHAR) with (
  type = 'random'
);

CREATE TABLE white_list (
  id VARCHAR,
  name VARCHAR,
  PRIMARY KEY (id),  -- The Row Key field in an ApsaraDB for Redis database.
  PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) WITH (
  type = 'redis',
  host = '<yourRedisHost>',
  password = '<yourRedisPassword>'
);

SELECT e.*, w. *
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id;
```

