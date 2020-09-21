# Blink-3.6.5

This topic lists the major improvements and bug fixes in Blink 3.6.5.

## Major improvements

-   Reduces the CPU utilization of DataHub source tables.
-   Improves the partition join feature of dimension tables, and supports loading data to partitions in dimension tables with the cache parameter set to ALL. In addition, the version supports the method of asynchronous parallel optimization for multiple dimension tables. For dimension tables that support asynchronous parallel optimization, you can enable the partition join feature by specifying partitionedJoin = 'true' in the WITH parameter.
-   Adds the startupMode parameter to Log Service source tables. The startupMode parameter has the following valid values:

    -   TIMESTAMP: indicates that you start to consume a shard from the specified time.
    -   Earlist: indicates that you start to consume a shard from the earliest data in the shard.
    -   Latest: indicates that you start to consume a shard from the latest data in the shard.
    -   Group\_Offsets: indicates that you start to consume a shard from a checkpoint that is stored in a specified consumer group.
    **Note:** The Timestamp, Earlist, Latest, and Group\_Offsets values take effect only when no checkpoint is available in the state.

-   Reduces the memory consumed to store vertex topology information for jobs that involve a large amount of data.
-   Supports multiple time formats for the timeFieldType parameter in Oracle source tables. The timeFieldType parameter has the following valid values:
    -   TO\_DATE: the DATE type.
    -   TIMESTAMP: the TIMESTAMP type.
    -   VARCHAR: the string type.
    -   NUMBER: the numeric type.
    -   Implements the OracleSourceQueryCondition interface and configures a class name for it.

## Major bug fixes

-   Fixes the bug that causes data errors in MaxCompute dimension tables if the Task Manager uses multiple threads and you enable the partition join feature.
-   Fixes the bug that causes error messages when the DDL field type of the sink node is different from the inserted data type.
-   Fixes the bug in which Blink does not consume data in Log Service source tables.

