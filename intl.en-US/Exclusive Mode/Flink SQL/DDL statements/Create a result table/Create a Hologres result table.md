---
keyword: create a Hologres result table
---

# Create a Hologres result table

This topic describes how to create a Hologres result table. It also describes the parameters in the WITH clause, streaming semantics, and data type mapping involved when you create a Hologres result table.

**Note:**

-   This topic applies only to Blink 3.6.0 and later. If your Blink version is earlier than 3.6.0, you can:
    -   Upgrade your Blink version to 3.6.0 or later. For more information, see [Manage Blink versions of a Realtime Compute for Apache Flink cluster deployed in exclusive mode](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Manage Blink versions of a Realtime Compute for Apache Flink cluster deployed in exclusive
         mode.md).
    -   [Submit a ticket](https://workorder-intl.console.aliyun.com/) to obtain the required JAR files for the upgrade.
-   We recommend that you use Hologres 0.7 or later.
-   Hologres writes data asynchronously. Therefore, you must add blink.checkpoint.fail\_on\_checkpoint\_error=true to the code so that a failover is triggered only when a job exception occurs.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and integrates seamlessly with the big data ecosystem. Hologres supports real-time analysis and the processing of petabytes of data with high concurrency and low latency. This allows you to use existing Business Intelligence \(BI\) tools to easily perform multidimensional analysis and business exploration.

## Syntax

In Realtime Compute for Apache Flink, you can use Hologres to store output data. The following code shows an example:

```
create table Hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='<yourDbname>',
  tablename='<yourTablename>',
  username='<yourUsername>',
  password='<yourPassword>',
  endpoint='<yourEndpoint>',
  field_delimiter='|' -- This parameter is optional.
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to hologres.|
|dbname|The name of the database.|Yes|None.|
|tablename|The name of the table.|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|endpoint|The VPC endpoint of Hologres.|Yes|For more information, see [Endpoints that are used to access Hologres](/intl.en-US/Manage instances/Endpoints that are used to access Hologres.md).|
|field\_delimiter|The delimiter used between rows when data is being exported. **Note:** Do not insert delimiters in data. This parameter takes effect only when the bulkload semantics is used.

|No|Default value: \\u0002.|
|mutateType|The streaming write semantics. For more information, see [Streaming semantics](#section_yce_507_nhr).|No|Default value: insertorignore.|
|partitionrouter|Specifies whether to write data into a partitioned table.|No|Default value: false.**Note:** For Blink 3.6.X, Hologres streaming sink cannot automatically create partitioned tables. Before Realtime Compute for Apache Flink writes data to a partitioned table, you must manually create a sub-table in Hologres. |
|ignoredelete|Specifies whether to skip retract messages.|No|Default value: false.**Note:** This parameter takes effect only when the streaming semantics is used. |
|createPartTable|Specifies whether a non-existent partitioned table can be automatically created based on partition values when Realtime Compute for Apache Flink writes data into a partitioned table.**Note:** If a partition value contains a hyphen \(-\), partitioned tables cannot be automatically created.

|No|-   false \(default value\): Partitioned tables cannot be automatically created.
-   true: Partitioned tables can be automatically created.

**Note:** Only Blink versions later than 3.7 support this parameter. |

## Streaming semantics

Stream processing, also known as streaming data or event processing, involves the processing of a series of unbounded data or events. A system that processes streaming data or events usually allows you to specify a reliability mode or processing semantics to ensure data accuracy because network or device faults may cause data loss.

Based on the configurations of Hologres streaming sink and Hologres table attributes, the following two types of semantics are supported:

-   Exactly-once: The system processes data or events only once even multiple faults occur.
-   At-least-once: If data or an event is lost before stream processing is completed, the system transfers all data and events again. In this case, data or events may be processed multiple times. If the first retry succeeds, no further retries are required.

When you use streaming semantics in a Hologres result table, take note of the following items:

-   If no primary keys have been configured in the Hologres physical table, Hologres streaming sink uses the at-least-once semantics.
-   If primary keys have been configured in the Hologres physical table, Hologres streaming sink uses the exactly-once semantics based on the primary keys. If multiple records with the same primary key are written to the table, you must set the mutationType parameter to determine how the result table is updated. This parameter has the following valid values:

    -   insertorignore \(default value\): Hologres keeps the first record and discards the subsequent records.
    -   insertorreplace: Hologres completely replaces the existing record with the one that arrives later.
    -   insertorupdate: Hologres partially replaces the existing record with the one that arrives later.
    **Note:**

    -   If the mutationType parameter is set to insertorupdate or insertorreplace, the system updates data based on the primary key.
    -   The number of columns in the result table defined by Blink can be different from the number of columns in the Hologres physical table. Make sure that the value null can be used to fill the missing columns. Otherwise, an error is returned.
-   By default, Hologres streaming sink can import data to only one non-partitioned table. If the sink imports data to the parent table of a partitioned table, data queries fail even if data import is successful. To enable data to be automatically written into a partitioned table, you can set the partitionRouter parameter to true. Take note of the following items:
    -   You must set tablename to the name of the parent table.
    -   Blink connectors do not automatically create partitioned tables. We recommend that you create a partitioned table before you import data. Otherwise, the data fails to be imported.

## Field type mapping

|Hologres|BLINK|
|--------|-----|
|INT|INT|
|INT\[\]|ARRAY<INT\>|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY<BIGINT\>|
|REAL|FLOAT|
|REAL\[\]|ARRAY<FLOAT\>|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY<DOUBLE\>|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY<BOOLEAN\>|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY<VARCHAR\>|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

