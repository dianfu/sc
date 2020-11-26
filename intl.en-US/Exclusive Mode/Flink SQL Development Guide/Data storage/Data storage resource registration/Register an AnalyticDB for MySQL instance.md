---
keyword: [AnalyticDB for MySQL, MySQL]
---

# Register an AnalyticDB for MySQL instance

This topic describes how to use Realtime Compute for Apache Flink to register an AnalyticDB for MySQL instance.

**Note:** This topic applies to only AnalyticDB for MySQL V2.0. Realtime Compute for Apache Flink does not allow you to register the AnalyticDB for MySQL V3.0 data store in the console to store result tables. To use result tables of AnalyticDB for MySQL V3.0, you must use the plaintext mode to create and reference the result tables. For more information, see [Create an AnalyticDB for MySQL V3.0 result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an AnalyticDB for MySQL V3.0 result table.md).

## Register storage resources

**Note:** Before you use Realtime Compute for Apache Flink to register storage resources, you must grant Realtime Compute for Apache Flink the permission to access these resources. For more information, see [t65125.md\#](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute in exclusive mode.md).

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane of the Development page, click **Storage**.
4.  In the upper-left corner of the Storage page, click **+Registration and Connection**.
5.  In the **Register Data Store and Test Connection** dialog box, configure the storage parameters.
6.  Click **OK**.

## Storage parameters

|Parameter|Description|
|---------|-----------|
|URL|The endpoint of AnalyticDB for MySQL. You can use the endpoint to log on to the AnalyticDB for MySQL console. Enter the AnalyticDB for MySQL endpoint based on your Realtime Compute for Apache Flink mode: -   If Realtime Compute for Apache Flink is deployed in shared mode, enter the classic network endpoint.
-   If Realtime Compute for Apache Flink is deployed in exclusive mode, enter the virtual private cloud \(VPC\) endpoint.

To view the endpoint, perform the following steps:

1.  Log on to the [AnalyticDB for MySQL console](https://ads.console.aliyun.com/?spm=a2c4g.11186623.2.23.2c952b809T8asM).
2.  In the left-side navigation pane, click Clusters. On the Clusters page, click the ID of the instance to go to the **Cluster Information** page.
3.  In the **Network Information** section, view the VPC endpoint. |
|Database|The name of the AnalyticDB for MySQL instance.|
|AccessKey ID|The AccessKey ID of your Alibaba Cloud account.|
|AccessKey Secret|The AccessKey secret of your Alibaba Cloud account.|

