---
keyword: create a Hologres source table
---

# Create a Hologres source table

This topic describes how to create a Hologres source table. It also describes the data definition language \(DDL\) syntax, parameters in the WITH clause, data type mapping, and sample code used when you create such a source table.

**Note:**

-   This topic applies only to Blink 3.6.0 and later. If your Blink version is earlier than 3.6.0, you can[submit a ticket](https://workorder-intl.console.aliyun.com/) to obtain the required JAR files for installation.
-   We recommend that you use Hologres 0.7 or later.
-   Only a database table that uses the row-oriented storage mode in Hologres can be used as a source table of Realtime Compute for Apache Flink.
-   Hologres source tables support the projection pushdown rule, which allows you to read data only from the specified columns in a source table.
-   Concurrent Blink jobs can read data from one or more Hologres shards. We recommend that the number of concurrent Blink jobs be no more than the number of Hologres shards.
-   You can use Hologres source tables for both stream processing and batch processing.
-   Snapshot statements are executed to read existing data from a Hologres source table at a high speed. After data reading is completed, the job is terminated. If data fails to read, the read operation is performed again.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and closely connected to the big data ecosystem. Hologres supports real-time analysis and processing of petabytes of data with high concurrency and low latency. This allows you to use existing Business Intelligence \(BI\) tools to easily perform multidimensional analysis and business exploration.

## DDL syntax

```
create table mysource(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='...',
  tablename='...',
  username='...',
  password='...',
  endpoint='...',
  field_delimiter='...' -- This parameter is optional.
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to hologres.|
|dbname|The name of the database.|Yes|None.|
|tablename|The name of the table.|Yes|None.|
|username|The username that is used to log on to the database. You must enter the AccessKey ID of your Alibaba Cloud account.|Yes|None.|
|password|The password that is used to log on to the database. You must enter the AccessKey secret of your Alibaba Cloud account.|Yes|None.|
|endpoint|The endpoint of Hologres.|Yes|For more information, see [Endpoints used to access Hologres](/intl.en-US/Manage instances/Endpoints used to access Hologres.md).|
|field\_delimiter|The delimiter used between rows when data is being exported. **Note:** Do not insert delimiters in data.

|Yes|Default value: \\u0002.|

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

## Sample code

The following sample code demonstrates how to create a Hologres source table in a Realtime Compute for Apache Flink job.

```
create table mysource(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='...',
  tablename='...',
  username='...',
  password='...',
  endpoint='...',
  field_delimiter='...' -- This parameter is optional.
);


create table print_output(
 a varchar,
 b BIGINT,
 c BIGINT 
) with (
  type='print'
);


INSERT INTO print_output
SELECT 
  a, b, c
from mysource;
```

