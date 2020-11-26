---
keyword: register a Tablestore instance
---

# Register a Tablestore instance

This topic describes how to use Realtime Compute for Apache Flink to register a Tablestore instance.

## Introduction to Tablestore

Tablestore is a NoSQL database service that is built based on Alibaba Cloud Apsara system. Tablestore allows you to store and access large amounts of structured data in real time. Tablestore provides the following benefits: low latency and simple computing. Therefore, Tablestore is suitable for storing dimension tables and result tables of Realtime Compute for Apache Flink.

## Register storage resources

**Note:** Before you use Realtime Compute for Apache Flink to register storage resources, you must grant Realtime Compute for Apache Flink the permission to access these resources. For more information, see [Grant a RAM role to Realtime Compute in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute in exclusive mode.md) .

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane of the Development page, click **Storage**.
4.  In the upper-left corner of the Storage page, click **+Registration and Connection**.
5.  In the **Register Data Store and Test Connection** dialog box, configure the storage parameters.
6.  Click **OK**.

## Parameters

-   Endpoint
    -   The virtual private cloud \(VPC\) endpoint of the Tablestore instance that you want to register. You can view this endpoint in the [Tablestore console](https://ots.console.aliyun.com). For more information about the VPC endpoint, see [VPC endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).
    -   To set Accessed By to **Any Network**, perform the following steps:
        1.  Log on to the [Tablestore console](https://ots.console.aliyun.com/).
        2.  In the left-side navigation pane, click All Instances. In the **Instance Name** column of the All Instances page, click the name of the Tablestore instance that you want to register.
        3.  On the Instant Management page, click the **Network Management** tab. On this tab, click **Change** next to Accessed By.
        4.  In the dialog box that appears, select **Any Network** from the Accessed By drop-down list.
        5.  Click **OK**.
-   Instance Name

    Enter the name of the Tablestore instance.


