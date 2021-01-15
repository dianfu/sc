---
keyword: [dimension table, MaxCompute]
---

# Create a MaxCompute dimension table

This topic describes how to create a MaxCompute dimension table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH and CACHE clauses and data type mapping used when you create a MaxCompute dimension table.

**Note:**

-   Blink 2.1.1 and later versions support MaxCompute dimension tables.
-   For more information about the query syntax of a dimension table, see [JOIN statements for dimension tables](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/JOIN statements for dimension tables.md).
-   To use a MaxCompute dimension table, you must grant the read permissions to the account used to access MaxCompute.

## DDL syntax

```
CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id), 
  PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) WITH (
  type = 'odps',
  endPoint = '<YourEndPoint>',
  project = '<YourProjectName>',
  tableName = '<YourtableName>',
  accessId = '<yourAccessKeyId>',
  accessKey = '<yourAccessKeySecret>',
  `partition` = 'ds=2018****',
  cache = 'ALL'
);       
```

**Note:**

-   When you declare a dimension table, you must specify a primary key. The primary key of a MaxCompute dimension table must be unique. Duplicate primary keys are deleted.
-   When you join a dimension table with another table, the ON condition must contain equivalence conditions that include all primary keys.
-   partition is a keyword and must be commented with backticks \('\), for example, `'partition'`.
-   If the dimension table is a partitioned table, Realtime Compute for Apache Flink does not write partition key columns to the DDL statement.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the dimension table.|Yes|Set the value to `odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md). **Note:** This parameter is required if MaxCompute is deployed in a VPC. |
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|partition|The name of a partition.|No|-   Fixed partition
    -   A MaxCompute table with only one partition

For example, if only one partition key column `ds` exists, ``partition` = 'ds=20180905'` indicates that the data in the `ds=20180905` partition is read.

    -   A MaxCompute table with multiple partitions

For example, if two partition key columns `ds` and `hh` exist, ``partition`='ds=20180905,hh=*'` indicates that the data in the `ds=20180905` partition is read.

**Note:** You must declare the values of all partitions when you filter partitions. In the preceding example, if you declare only ``partition` = 'ds=20180905'`, no partition is read.

-   Non-fixed partition
    -   Blink 2.2.0 and later versions support `'partition' = 'max _pt()'`. This setting indicates that the partition ranked first in alphabetical order among all partitions is loaded each time the system loads data of partitions.
    -   Blink 3.2.2 and later versions support `'partition' = 'max_pt_with_done()'`. This setting indicates that the partition with the file name extension `.done` and ranked first in alphabetical order among all partitions is loaded each time the system loads data of partitions. |
|maxRowCount|The maximum number of rows that Realtime Compute for Apache Flink can load from a table.|No|Default value: 100000. **Note:** If your data contains more than 100,000 rows, you must set this parameter. We recommend that you set this parameter to a greater value than the actual number of rows to be loaded. |

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|You must set the cache parameter to `ALL` for a MaxCompute dimension table and explicitly declare this setting in the DDL statement. ALL: indicates that all data in the dimension table is cached. Before a Realtime Compute for Apache Flink job runs, Realtime Compute for Apache Flink loads all the data in the dimension table to the cache. Then, Realtime Compute for Apache Flink searches among the cache if requests are sent to retrieve data from the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system reloads all data in the cache after cache entries expire.

If the data amount of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\) If you set this parameter to ALL, you must specify the following parameters: cacheTTLMs and cacheReloadTimeBlackList.

**Note:**

-   If the cache parameter is set to ALL, you must increase the memory of the join node because the system asynchronously loads data of the dimension table. We recommend that you increase the size of the memory at least four times the data amount of the remote table. The value is related to the MaxCompute storage compression algorithm.
-   If a job exception occurs due to frequent garbage collection when you use a super-large MaxCompute dimension table, and this issue persists even if you increase the memory of the join node, we recommend that:
    -   For Blink 3.6.0 and later versions, set the partitionedJoin parameter to true to enable partitionedJoin optimization.
    -   Use a dimension table with key-value pairs for which you can set the cache parameter to LRU, for example, an ApsaraDB for HBase dimension table. |
|cacheSize|The maximum number of data records that can be cached.|You can specify the cacheSize parameter based on your business requirements. By default, Realtime Compute for Apache Flink can cache 100,000 rows that are stored in a MaxCompute dimension table.|
|cacheTTLMs|The cache timeout period.|Unit: milliseconds. If you set the cache parameter to `ALL`, the timeout period specifies the interval at which Realtime Compute for Apache Flink refreshes the cache. The cache is not refreshed by default.|
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for large-scale online promotional events such as Double 11.|This parameter is empty by default. The following example shows the format of the values: `2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`. The following list describes the delimiters that you use for the parameter values: -   Separate multiple time periods with commas \(`,`\).
-   Separate the start time and end time of each time period with a hyphen and a greater-than sign \(`->`\). |
|partitionedJoin|Specifies whether to cache full data of a dimension table in the memory of each concurrent job.|This parameter is optional. Default value: false. This value indicates that full data of the dimension table is cached in the memory of each concurrent job. If you set this parameter to true, partial data of the dimension table is cached in the memory of each concurrent task.|

## Sample code

The following sample code demonstrates how to create a MaxCompute dimension table in a Realtime Compute for Apache Flink job.

```
CREATE TABLE datahub_input1 (
   id    BIGINT,
   name  VARCHAR,
   age   BIGINT
) with (
   type='datahub'
);


CREATE TABLE odps_dim (
   name VARCHAR,
   phoneNumber BIGINT, 
   PRIMARY KEY (name), 
   PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
) with (
   type = 'odps',
   endPoint = '<yourEndpointName>', 
   project = '<yourProjectName>',
   tableName = '<yourTableName>',
   accessId = '<yourAccessId>',
   accessKey = '<yourAccessPassword>',
  `partition` = 'ds=20180905',--The fixed partition. For more information about dynamic partitions and fixed partitions, see the description of parameters in the WITH clause.
  cache = 'ALL'
);

CREATE table result_infor(
  id BIGINT,
  phoneNumber BIGINT,
  name VARCHAR
)with(
  type='print'
);

INSERT INTO result_infor
SELECT
  t.id,
  w.phoneNumber,
  t.name
FROM datahub_input1 as t
JOIN odps_dim FOR SYSTEM_TIME AS OF PROCTIME() as w --You must include this clause when you perform a JOIN operation on a dimension table.
ON t.name = w.name;
```

## Field type mapping

The following table lists the mapping between the data types of MaxCompute and Realtime Compute for Apache Flink \(fully-managed Flink\). We recommend that you declare the mapping in a DDL statement.

|MaxCompute|BLINK|
|----------|-----|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

**Note:** Realtime Compute for Apache Flink supports only the preceding data types in MaxCompute dimension tables.

## FAQ

-   What is the difference between `max_pt()` and `max_pt_with_done()`?

    `max_pt()` indicates that the partition ranked first in alphabetical order among all partitions is loaded. `max_pt_with_done()` indicates that the partition with the file name extension `.done` and ranked first in alphabetical order among all partitions is loaded.

    |Partition|
    |---------|
    |ds=20190101|
    |ds=20190101.done|
    |ds=20190102|
    |ds=20190102.done|
    |ds=20190103|

    This example shows the partitions that correspond to `max_pt()` and `max_pt_with_done()`:

    -   The partition that corresponds to ``partition`='max_pt_with_done()'` is `ds=20190102`.
    -   The partition that corresponds to ``partition`='max_pt()'` is `ds=20190103`.
    **Note:** Only Blink 3.3.2 and later versions support `'partition' = 'max_pt_with_done()'`.

-   Q: The failover message `RejectedExecutionException: Task java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTas` is reported when a job is running. What do I do?

    A: Dimension table joining in Blink 1.0 has some issues. We recommend that you upgrade the Blink version to 2.1.1 or later. If you still want to use Blink 1.0, you must suspend your job and then resume it. You can troubleshoot this issue based on the first error message in the failover history.

-   Q: What do the endPoint and tunnelEndpoint parameters mean in the Alibaba Cloud public cloud? What happens if the parameter configurations are invalid?

    A: For more information about the endPoint and tunnelEndpoint parameters, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md). If the configuration of these two parameters is incorrect in a VPC, one of the following task exceptions may occur.

    -   If the configuration of the endPoint parameter is invalid, the task stops at a progress of 91%.
    -   If the configuration of the tunnelEndpoint parameter is invalid, the task fails.

