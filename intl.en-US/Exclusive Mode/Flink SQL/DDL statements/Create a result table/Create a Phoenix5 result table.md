---
keyword: Phoenix5 result table
---

# Create a Phoenix5 result table

This topic describes how to create a Phoenix5 result table in Realtime Compute for Apache Flink.

**Note:**

-   This topic applies only to Realtime Compute for Apahce Flink in exclusive node.
-   This topic applies only to Blink 3.4.0 and later.
-   Only Phoenix 5.X is supported.
-   Phoenix is an HBase SQL service deployed on an ApsaraDB for HBase instance. You can use Phoenix only after you activate this service in ApsaraDB for HBase instances.

## DDL syntax

In Realtime Compute for Apache Flink, you can use Phoenix5 to store output data. The following code shows an example:

```
create table US_POPULATION_SINK (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY)--- The primary key. This field is required.
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<yourserverUrl>',
  tableName = '<yourTableName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to `PHOENIX5`.|
|serverUrl|The address of the Phoenix5 Query Server.-   If Phoenix5 is created in a cluster, the URL of Server Load Balancer \(SLB\) is used.
-   If Phoenix5 is created on a server, the URL of the server is used.

|Yes|You must activate the HBase SQL service in an ApsaraDB for HBase instance..|
|tableName|The name of the Phoenix5 table.|Yes|None.|

## Sample code

The following sample code demonstrates how to create a Phoenix5 result table in a Realtime Compute for Apache Flink job.

```
create table `source` (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar 
) WITH (
   type = 'random'
);

create table sink (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar,
  primary key (id)
) WITH (
  type = 'PHOENIX5',
  serverUrl = '<yourserverUrl>',
  tableName = '<yourTableName>'
);

INSERT INTO sink
  SELECT  `id` ,`name` , `age` ,`birthday` 
FROM `source`;
```

