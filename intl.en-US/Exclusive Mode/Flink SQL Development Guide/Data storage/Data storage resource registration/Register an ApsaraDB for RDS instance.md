# Register an ApsaraDB for RDS instance

This topic describes how to use Realtime Compute for Apache Flink to register an ApsaraDB for RDS instance.

## Introduction to ApsaraDB for RDS

ApsaraDB for RDS is a stable, reliable, and scalable online database service. Based on Apsara Distributed File System and high-performance storage services, ApsaraDB for RDS supports a wide range of database engines, such as MySQL, SQL Server, PostgreSQL, and Postgres Plus Advanced Server \(PPAS\). ApsaraDB for RDS provides comprehensive solutions for database operations and maintenance \(O&M\), such as disaster recovery, data backup, data recovery and restoration, monitoring, and data migration.

**Note:**

-   If Realtime Compute for Apache Flink frequently writes data to a table or a resource file, a deadlock may occur. In scenarios that require highly frequent or highly concurrent writes, we recommend that you use Tablestore to store result tables. For more information, see [Create a Tablestore result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create a Tablestore result table.md).
-   Realtime Compute for Apache Flink does not allow you to register the ApsaraDB for RDS V8.0 data store in the console to store result tables. To use result tables of ApsaraDB for RDS V8.0, you must use the plaintext mode to create and reference the result tables. For more information, see [Use a plaintext AccessKey pair](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Overview.md).

## Register storage resources

**Note:** Before you use Realtime Compute for Apache Flink to register storage resources, you must grant Realtime Compute for Apache Flink the permission to access these resources. For more information, see [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md).

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane of the Development page, click **Storage**.
4.  In the upper-left corner of the Storage page, click **+Registration and Connection**.
5.  In the **Register Data Store and Test Connection** dialog box, configure the storage parameters.
6.  Click **OK**.

## Parameters

**Note:** When you register storage resources in an ApsaraDB for RDS instance, the IP addresses of Realtime Compute for Apache Flink are automatically added to the whitelist for accessing the ApsaraDB for RDS instance.

|Parameter|Description|
|---------|-----------|
|**Storage Type**|The type of storage resources that you want to register. From the Storage Type drop-down list, select **RDS Data Storage**.|
|**Region**|The region where the ApsaraDB for RDS instance resides.|
|**Instance**|The ID of the ApsaraDB for RDS instance that you want to register. **Note:** Enter the instance ID instead of the instance name. |
|**DBName**|The name of the ApsaraDB for RDS database. **Note:** Enter the database name instead of the instance name. |
|**User Name**|The username that is used to access the ApsaraDB for RDS database.|
|**Password**|The password that is used to access the ApsaraDB for RDS database.|

