# JOIN statements for dimension tables

In Realtime Compute for Apache Flink, each data stream can be associated with an dimension table that is stored in an external data source. This allows you to perform associated queries in Realtime Compute for Apache Flink.

**Note:** A dimension table constantly changes. Therefore, when you associate a data stream with a dimension table, you must specify the time of the dimension table snapshot with which the data stream is associated. A data stream can be associated with only the dimension table snapshot that is taken at the current time. In the future, Realtime Compute for Apache Flink will allow you to associate data streams with dimension table snapshots that are taken at different points in time. The points in time are specified by the rowtime field in the left table. For more information about dimension tables, see [Dimension table overview](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a dimension table/Dimension table overview.md).

## Syntax

```
SELECT column-names
FROM table1  [AS <alias1>]
[LEFT] JOIN table2 FOR SYSTEM_TIME AS OF PROCTIME() [AS <alias2>]
ON table1.column-name1 = table2.key-name1;
```

In the following example, an event stream is joined with a whitelist dimension table.

```
SELECT e.*, w. *
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id;
```

**Note:**

-   Dimension tables support `INNER JOIN` and `LEFT JOIN` operations, and do not support `RIGHT JOIN` or `FULL JOIN` operations.
-   You must append `FOR SYSTEM_TIME AS OF PROCTIME()` to the end of the dimension table. This way, each data record in the dimension table that can be viewed at the current time is associated with the source data.
-   The subsequent input data in the source table is associated with only the latest records that are stored in the dimension table at the current time. This means that the JOIN operation is performed only at the processing time. Therefore, if the data in the dimension table is added, updated, or deleted after the JOIN operation is performed, the associated data remains unchanged.
-   The ON clause must contain the equivalent \(=\) conditions for all the primary key fields of the dimension table. The primary key fields in the conditions must be the same as those in the physical tables that are referenced in the SQL statement. The ON clause can also contain other equivalent \(=\) conditions.
-   If you want to perform one-to-many table joins, you must specify the join keys in the data definition language \(DDL\) INDEX syntax for dimension tables. For more information, see[INDEX syntax](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a dimension table/Dimension table overview.md).
-   Two dimension tables cannot be joined.
-   In the join conditions that are specified in the ON clause, the fields in the dimension table cannot use type conversion functions such as CAST. If you need to convert data types, perform the conversion on the fields in the source table.

## Examples

-   Test data

    |id \(BIGINT\)|name \(VARCHAR\)|age \(BIGINT\)|
    |-------------|----------------|--------------|
    |1|lilei|22|
    |2|hanmeimei|20|
    |3|libai|28|

    |name \(VARCHAR\)|phoneNumber \(BIGINT\)|
    |----------------|----------------------|
    |dufu|18867889855|
    |baijuyi|18867889856|
    |libai|18867889857|
    |lilei|18867889858|

-   Test statement

    ```
    CREATE TABLE datahub_input1 (
    id            BIGINT,
    name        VARCHAR,
    age           BIGINT
    ) WITH (
    type='datahub'
    );
    
    create table phoneNumber(
    name VARCHAR,
    phoneNumber bigint,
    primary key(name),
    PERIOD FOR SYSTEM_TIME
    )with(
    type='rds'
    );
    
    CREATE table result_infor(
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
    JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w
    ON t.name = w.name;
    ```

-   Test result

    |id \(BIGINT\)|phoneNumber \(BIGINT\)|name \(VARCHAR\)|
    |-------------|----------------------|----------------|
    |1|18867889858|lilei|
    |3|18867889857|libai|


