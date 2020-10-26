# Blink 3.4.4

This topic lists the major features and bug fixes in Blink 3.4.4.

## Major improvements

Blink 3.4.4 supports reading data from the new partitions of MaxCompute source tables. You can specify the subscribeNewPartition parameter in the DDL statement to enable or disable this feature. If the subscribeNewPartition parameter is set to true and the partition parameter is not specified, the system constantly reads data from the new partitions of MaxCompute source tables. For more information, see [Create a full MaxCompute source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Create a full MaxCompute source table.md).

## Major bug fixes

Fixes ApsaraDB RDS connection issues. Blink uses the Druid database connection pool to connect to ApsaraDB RDS. If the connection is not used for a long period of time, job failovers may occur. To avoid these issues, Blink 3.4.4 optimizes the method of connecting the Druid connection pool to ApsaraDB RDS.

