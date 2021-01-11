# Overview

Alibaba Cloud Realtime Compute provides a management page for various storage systems, such as ApsaraDB for RDS and Tablestore. It offers you a one-stop cloud-based management solution.

## Data storage in Realtime Compute

In Realtime Compute, data storage has the following two meanings:

-   It refers to the storage systems or database tables \(hereinafter referred to as storage resources\) at the upstream and downstream nodes of Realtime Compute.
-   It also refers to how Realtime Compute manages the upstream and downstream storage resources \(hereinafter referred to as the data storage feature\).

**Note:** Before you register data storage resources with Realtime Compute, you must authorize Realtime Compute to access these resources. For more information, see [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md)

Realtime Compute allows you to reference both upstream and downstream storage resources by using plaintext AccessKey pairs or registering data storage resources.

## Use a plaintext AccessKey pair

To explicitly reference upstream and downstream storage resources, you must set the `accessId` and `accessKey` parameters in the WITH clause of the related Data Definition Language \(DDL\) statement. For more information, see [Overview](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Overview.md). In this mode, you can authorize an Alibaba Cloud account and its RAM users to access the resources of the current or another Alibaba Cloud account. Assume that user A or a RAM user created under the Alibaba Cloud account of user A wants to use the storage resources of user B. User A can set the AccessKey pair of user B in the following DDL statement in plaintext mode:

```
CREATE TABLE in_stream(
  a varchar,
  b varchar,
  c timestamp
)with(
type='datahub',
  endPoint='http://dh-cn-hangzhou.aliyuncs.com',
  project='<dataHubProjectName>',
  topic='<dataHubTopicName>',
  accessId='<accessIdOfUserB>',
  accessKey='<accessKeyOfUserB>'
);
```

## Register a storage resource

Realtime Compute allows you to manage and reference both upstream and downstream storage resources that have been registered with the Realtime Compute development platform. After storage resources are registered, you can preview or sample the relevant data, or obtain the DDL statements that are automatically generated to reference the resources. This helps you manage your cloud-based storage resources in one-stop mode.

**Note:** You can only register storage resources of the current Alibaba Cloud account. Therefore, user A or a RAM user created under the Alibaba Cloud account of user A can only register storage resources purchased by user A. To use storage resources of another Alibaba Cloud account, set the AccessKey pair of the specified Alibaba Cloud account in the relevant DDL statement.

-   Register storage resources

    To register upstream and downstream storage resources with the Realtime Compute development platform before you reference them, follow these steps:

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
    2.  In the top navigation bar, click **Development**.
    3.  In the left-side navigation pane of the Development page, click **Storage**.
    4.  In the upper-right corner of the **Storage** tab, click **+Registration and Connection**.
    5.  In the **Register Data Store and Test Connection** dialog box that appears, set parameters for the storage resources as required.

        Realtime Compute allows you to register the following three types of storage resources. For more information about how to register a specific type of storage resources, click the following link that corresponds to the type:

        -   [Register a Tablestore instance](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register a Tablestore instance.md)
        -   [Register an ApsaraDB for RDS instance](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an ApsaraDB for RDS instance.md)
        -   [Register a Log Service project](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register a Log Service project.md)
-   Preview data

    To preview data from registered storage resources, follow these steps:

    1.  In the left-side navigation pane of the Development page, click **Storage**.
    2.  On the **Storage** tab, navigate through the cascaded folders and nodes to find the target table, and then double-click the name of the table.
    3.  In the **Table Details** pane that appears, view data of the storage resource in the **Data Preview** section.
    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9393276061/p33024.png)

-   Obtain the DDL statements that are automatically generated to reference a storage resource

    To obtain the DDL statements that are automatically generated to reference a storage resource, follow these steps:

    1.  In the left-side navigation pane of the Development page, click **Storage**.
    2.  On the **Storage** tab, navigate through the cascaded folders and nodes to find the target table, and then double-click the name of the table.
    3.  In the **Table Details** pane that appears, click **Reference as Source Table**, **Reference as Result Table**, or **Reference as Dimension Table** as required. Then, you can obtain the DDL statements that are automatically generated to reference the target table.
    **Note:** The automatically generated DDL statements contain only the basic parameters in the WITH clause to ensure the smooth connection between Realtime Compute and storage resources. You can add other parameters to the WITH clause as required.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9393276061/p33025.png)

-   Test network connectivity by using the network detection feature

    **Note:** The network detection feature is not supported in the China \(Hangzhou\) region of Alibaba Finance Cloud because Cloud Assistant is not installed.

    Realtime Compute offers the network detection feature for data storage. This feature allows you to test network connectivity between Realtime Compute and storage resources. To enable the network detection feature, follow these steps:

    1.  In the left-side navigation pane of the Development page, click **Storage**.
    2.  In the upper-right corner of the **Storage** tab, click **+Registration and Connection**.
    3.  In the **Register Data Store and Test Connection** dialog box, turn on **Test Connection**.
    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3221659951/p33652.png)


