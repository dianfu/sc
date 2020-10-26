# Blink 3.6.5

This topic describes major improvements and bug fixes in Blink 3.6.5.

## Major improvements

-   Reduces the CPU utilization of DataHub source tables.
-   Improves the partition join feature of dimension tables, and supports the loading of data to partitions in dimension tables with the cache parameter set to ALL. This version supports asynchronous parallel optimization for multiple dimension tables. You can set the partitionedJoin parameter to true in the WITH parameter to enable the partition join feature for dimension tables that support asynchronous parallel optimization.
-   Adds the startupMode parameter to Log Service source tables. The startupMode parameter has the following valid values:

    -   TIMESTAMP: indicates that data of a shard is consumed from a specified time.
    -   Earliest: indicates that data of a shard is consumed from the earliest data in the shard.
    -   Latest: indicates that data of a shard is consumed from the latest data in the shard.
    -   Group\_Offsets: indicates that data of a shard is consumed from a checkpoint that is stored in a specified consumer group.
    **Note:** The values of TIMESTAMP, Earliest, Latest, and Group\_Offsets take effect only when no checkpoint exists in the state.

-   Reduces the memory consumed to store vertex topology information for jobs that involve a large amount of data.
-   Supports multiple time formats for the timeFieldType parameter in Oracle source tables. The timeFieldType parameter has the following valid values:
    -   TO\_DATE: the DATE type.
    -   TIMESTAMP: the TIMESTAMP type.
    -   VARCHAR: the DATETIME string type.
    -   NUMBER: the NUMERIC type.
    -   Supports the OracleSourceQueryCondition interface and configures a class name for it.

## Major bug fixes

-   Fixes the bug that data errors occur in MaxCompute dimension tables if a TaskManager uses multiple threads and the partition join feature is enabled.
-   Fixes the bug that an error message is reported when the DDL field type of the sink node is different from the type of the inserted data.
-   Fixes the bug that Realtime Compute for Apache Flink does not consume data in Log Service source tables.

