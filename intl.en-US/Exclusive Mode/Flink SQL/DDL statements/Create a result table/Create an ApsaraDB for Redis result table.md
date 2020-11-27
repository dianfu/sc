# Create an ApsaraDB for Redis result table

This topic describes how to create an ApsaraDB for Redis result table in Realtime Compute for Apache Flink. This topic also describes the parameters in the WITH clause, the mappings between the field data types of ApsaraDB for Redis and Realtime Compute for Apache Flink, and the attribute fields. You may need to use these items when you create the table.

**Note:**

-   This topic applies to only Blink V3.2.0 and later.
-   User-created Redis databases can be used to store the result tables in the ApsaraDB for Redis databases in Realtime Compute for Apache Flink.

## What is ApsaraDB for Redis?

ApsaraDB for Redis is a database service that is compatible with the protocols of the open source Redis system. It supports a hybrid of memory and hard disks for storage. ApsaraDB for Redis provides a hot standby architecture to ensure high availability. Based on the scalable cluster architecture, ApsaraDB for Redis can meet the business requirements for high throughputs, low-latency operations, and flexible configuration changes. Realtime Compute for Apache Flink allows you to store the output streaming data in ApsaraDB for Redis.

## Syntax

You can use five data types when you write data to ApsaraDB for Redis result tables. To create ApsaraDB for Redis result tables, execute the following data definition language \(DDL\) statements:

-   STRING type

    Each DDL statement for the STRING data type contains two columns. The first column specifies the key and the second column specifies the value. To insert data into an ApsaraDB for Redis result table, run the `set key value` command.

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'string',
      host = '${redisHost}', -- An example value is '127.0.0.1'.
      port = '${redisPort}', -- An example value is '6379'.
      dbNum = '${dbNum}', -- The default value is 0.
      ignoreDelete = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   LIST type

    Each DDL statement for the STRING data type contains two columns. The first column specifies the key and the second column specifies the value. To insert data into an ApsaraDB for Redis result table, run the `lpush key value` command.

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'list',
      host = '${redisHost}', -- An example value is '127.0.0.1'.
      port = '${redisPort}', -- An example value is '6379'.
      dbNum = '${dbNum}', -- The default value is 0.
      ignoreDelete = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   SET type

    Each DDL statement for the STRING data type contains two columns. The first column specifies the key and the second column specifies the value. To insert data into an ApsaraDB for Redis result table, run the `sadd key value` command.

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'set',
      host = '${redisHost}', -- An example value is '127.0.0.1'.
      port = '${redisPort}', -- An example value is '6379'.
      dbNum = '${dbNum}', -- The default value is 0.
      ignoreDelete = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   HASHMAP type

    A DDL statement has three columns. The first column is the key, the second column is the hash key, and the third column is the hash value. To insert data into an ApsaraDB for Redis result table, run the `hmset key hash_key hash_value` command.

    ```
    create table resik_output (
      a varchar,
      b varchar, 
      c varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'hashmap',
      host = '${redisHost}', -- An example value is '127.0.0.1'.
      port = '${redisPort}', -- An example value is '6379'.
      dbNum = '${dbNum}', -- The default value is 0.
      ignoreDelete = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   SORTEDSET type

    A DDL statement has three columns. The first column is the key, the second column is the score, and the third column is the value. To insert data into an ApsaraDB for Redis result table, run the `add key score value` command.

    ```
    create table resik_output (
      a varchar,
      b double,  -- The data must be of the DOUBLE type.
      c varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'sortedset',
      host = '${redisHost}', -- An example value is '127.0.0.1'.
      port = '${redisPort}', -- An example value is '6379'.
      dbNum = '${dbNum}', -- The default value is 0.
      ignoreDelete = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```


## Parameters in the WITH clause

|Parameter|Description|Required|Value|
|---------|-----------|--------|-----|
|type|The type of the result table.|Yes|Set the value to `redis`.|
|mode|The data type of the ApsaraDB for Redis result table.|Valid values: -   string
-   list
-   set
-   hashmap
-   sortedset |
|host|The endpoint of ApsaraDB for Redis.|Example: `127.0.0.1`.|
|port|The port of the ApsaraDB for Redis database.|No|Default value: 6379.|
|dbNum|The sequence number of the ApsaraDB for Redis database.|Default value: 0.|
|ignoreDelete|Specifies whether to ignore the retraction message.|Valid values: true and false. Default value: false. If this parameter is set to false, the inserted data and the keys of the data are deleted when a retraction message is received.|
|password|The password that is used to access the ApsaraDB for Redis database.|By default, this parameter is empty. This indicates that permission verification is not required.|

## Field type mappings

The following table describes the mappings between the field data types of ApsaraDB for Redis and Realtime Compute for Apache Flink. We recommend that you declare the mappings in DDL statements.

|ApsaraDB for Redis field type|Realtime Compute for Apache Flink field type|
|-----------------------------|--------------------------------------------|
|STRING|VARCHAR|
|SCORE|DOUBLE|

**Note:** The data of the SCORE type is added to the values of the SORTEDSET data type in ApsaraDB for Redis databases. You must manually set a score of the DOUBLE type for each sorted set value and sort the values based on their scores in ascending order.

## Sample code

For a Realtime Compute for Apache Flink job, you use the following sample code to create an ApsaraDB for Redis result table.

```
CREATE TABLE random_stream (
  v VARCHAR, 
  p VARCHAR) with (
    type = 'random'
);

create table resik_output (
  a VARCHAR,
  b VARCHAR,
  primary key(a) 
) with (
  type = 'redis',
  mode = 'string',
  host = '<yourRedisHost>',
  password = '<yourRedisPassword>'
);

INSERT INTO resik_output 
SELECT v, p
FROM random_stream;
```

