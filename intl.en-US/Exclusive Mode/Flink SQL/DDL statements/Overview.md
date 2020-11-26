# Overview

This topic describes the data definition language \(DDL\) syntax that is supported by Realtime Compute for Apache Flink, and the field mapping and case sensitivity issues that require your attention when you use DDL.

## Syntax

```
CREATE TABLE tableName
      (columnName dataType [, columnName dataType ]*)
      [ WITH (propertyName=propertyValue [, propertyName=propertyValue ]*) ];
```

## Description

Realtime Compute for Apache Flink does not store data. All the DDL statements that are executed to create tables declare references to external tables and data stores.

```
CREATE TABLE mq_stream(
 a VARCHAR,
 b VARCHAR,
 c VARCHAR
) WITH (
 type='mq',
 topic='blink_mq_test',
 accessID='<yourAccessID>',
 accessKey='<yourAccessSecret>'
);
```

The preceding code does not create a topic of the Message Queue source table in Flink SQL. Instead, the code is used to declare a reference to the `mq_stream` table. For all the downstream data manipulation language \(DML\) operations on the Message Queue topic `blink_mq_test`, you can replace the topic name with the alias `mq_stream`. When you declare references to external tables, pay attention to the following points:

-   In Realtime Compute for Apache Flink, a declaration of a table is valid only for the current job. A Realtime Compute for Apache Flink job is generated after you submit an SQL file. Therefore, the preceding declaration related to the `mq_stream` table is valid only for the current SQL file. Different SQL files in the same Realtime Compute for Apache Flink project can declare reference of the `mq_stream` table.
-   Based on the standard SQL definitions, keywords, table names, and column names in DDL statements are not case-sensitive.
-   The names of tables and columns must start with a letter, and can contain only letters, digits, and underscores \(\_\).
-   DDL declarations may establish the field mappings between the declaration table and the external table based on field names or other factors. This depends on the nature of the upstream plug-in that is used. To prevent data errors caused by inaccurate definitions, we recommend that you use the same field names and field quantity in your declaration as those in the referenced external tables.

    **Note:**

    -   If upstream and downstream plug-ins support retrieving values based on keys, the declared table and its referenced external table can have different field quantity. However, the field names must be the same.
    -   If the upstream and downstream plug-ins do not support retrieving values based on keys, the declaration table and its referenced external table must have the same number and sequence of fields.

## Field mapping

A declared table supports the following two field mapping methods based on whether its external data source has a schema:

-   Sequence mapping

    This method applies to data sources that have no schema, such as Message Queue. These data sources are usually unstructured storage systems that do not support retrieving values based on keys. We recommend that you customize field names in DDL SQL statements and use the same field types and field quantity in the declared table as those in the external table.

    The following record in Message Queue is used as an example:

    ```
    asavfa,sddd32,sdfds
    ```

    Specify Message Queue field names based on the naming conventions.

    ```
    CREATE TABLE mq_stream(
     a VARCHAR,
     b VARCHAR,
     c VARCHAR
    ) WITH (
     type='mq',
     topic='blink_mq_test',
     accessID='<yourAccessID>',
     accessKey='<yourAccessSecret>'
    );
    ```

-   Name mapping

    This method applies to data sources that have a schema. These data sources define field names and field types at the table storage level and support retrieving values based on keys. We recommend that you use the same schema definitions in Flink SQL declarations as those of the external storage system. Specifically, the names, number, and sequence of fields in the declaration table must be the same as those in the external table.

    **Note:** If field names in an external storage system such as [Tablestore](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create a Tablestore result table.md) are case-sensitive, you must enclose the field names in grave accents \(\`\). In the DDL syntax, field names in the declaration table must be the same as those in the external table.


## Case sensitivity

Realtime Compute for Apache Flink adopts standard SQL statements. Therefore, fields are not case-sensitive. For example, the following two statements have the same meaning:

```
create table stream_result (
    name varchar,
    value varchar
);
```

```
create table STREAM_RESULT (
    NAME varchar,
    VALUE varchar
);
```

However, fields in some external data sources that are referenced by Realtime Compute for Apache Flink are case-sensitive, such as Tablestore. The following statement defines the uppercase `NAME` field for Tablestore.

```
create  table STREAM_RESULT (
    `NAME` varchar,
    `VALUE` varchar
);
```

In all subsequent DML statements, enclose the field in grave accents \(\`\) if the field is referenced. In the following example, the sample code is provided.

```
INSERT INTO tableA
SELECT
  `NAME`,
  `VALUE`
FROM
  tableB;
```

## References

For more information about how to create source tables, dimension tables, and result tables in Realtime Compute for Apache Flink, see the following topics:

-   [Overview of source tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md)
-   [Overview of result tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Overview of result tables.md)
-   [Dimension table overview](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a dimension table/Dimension table overview.md)

