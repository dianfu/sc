# Activate Realtime Compute for Apache Flink and create a project

This topic describes how to activate Realtime Compute for Apache Flinkin exclusive mode and how to createRealtime Compute for Apache Flink clusters and projects in exclusive mode.

-   A Realtime Compute for Apache Flink cluster in exclusive mode can access only storage resources in the same VPC, region, and security group as the cluster. To allow the cluster to access resources in another VPC, use [Express Connect](/intl.en-US/Product Introduction/What is Express Connect?.md) to access the network.
-   Role authorization for Realtime Compute for Apache Flink in exclusive mode is completed. For more information, see [Grant a RAM role to Realtime Compute in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute in exclusive mode.md).

## Activate Realtime Compute for Apache Flink in exclusive mode and create a cluster and project

1.  Activate Realtime Compute for Apache Flink in exclusive mode.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://www.alibabacloud.com/zh/products/realtime-compute?spm=a2796.7919406.3156523820.dnavproductdatacom3.66f42d23uPyhJv).

    2.  Click **Buy Now**.

    3.  Enter configuration information, such as the region, cluster configuration, and subscription duration.

    4.  Click **Buy Now**.

        **Note:** For more information about the cluster configuration, see [Specification selection](/intl.en-US/Exclusive Mode/Pricing/Specification selection.md).

    5.  Read and select **I have read and agree to Realtime Compute Exclusive Mode \(Subscription\) Agreement of Service**.

    6.  Click **Pay**.

    7.  Click **Pay**.

2.  Create a cluster.

    1.  After you complete the payment, click **Console**.

    2.  On the **Clusters** page, click **Create Cluster**.

        **Note:** If you have not created a project for an order, a red number is displayed in the upper-right corner of **Create Cluster** on the **Clusters** page. The number is the number of orders for which you have not created a project.

    3.  Specify **Order ID** and click **Next**.

    4.  Enter **Cluster Name** and **Cluster Remarks**, and then click **Next**.

    5.  In the **Cluster Settings** step, enter configuration information and click Next.

        -   **OSS Bucket**

            You can upload UDF packages for a Realtime Compute for Apache Flink cluster in exclusive mode. To ensure data security, Realtime Compute for Apache Flink stores the UDF packages in an Object Storage Service \(OSS\) bucket. You must specify an existing OSS bucket. If you do not have an OSS bucket, create one first. For more information about how to create an OSS bucket, see [Create buckets](/intl.en-US/Quick Start/Create buckets.md).

            When you create an OSS bucket, you must specify **Standard** for **Storage Class**. We recommend that you specify **Private** for **Access Control List \(ACL\)**. Do not select **Public Read**.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4764076061/p33098.png)

        -   **VPC**

            If you have VPCs, specify a VPC for Realtime Compute for Apache Flink. If you do not have a VPC, activate the Alibaba Cloud VPC service first. For more information about how to activate the VPC service, see [Plan and design a VPC](/intl.en-US/Quick Start/Plan and design a VPC.md).

            When you create a VPC, make sure that:

            -   Sufficient Elastic Compute Service \(ECS\) instances are available in the VPC. For more information about how to create an ECS instance, see [Create an instance by using the wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the wizard.md).
            -   The number of available IP addresses in a VSwitch is greater than or equal to the number of nodes in a Realtime Compute for Apache Flink cluster. For more information, see [View the nodes of a Realtime Compute for Apache Flink cluster](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md) and [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md).
        -   **Zone**

            After you properly configure the VPC, the system automatically displays the available zones.

            **Note:** No zone or VSwitch is available in the following scenarios:

            -   ECS instances in the zone are insufficient. For more information about how to add an ECS instance, see [Create an instance by using the wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the wizard.md).
            -   The number of available IP addresses in the selected VSwitch is less than the number of nodes in a Realtime Compute for Apache Flink cluster. For more information, see [View the nodes of a Realtime Compute for Apache Flink cluster](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md) and [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md).
    6.  In the **Confirm** step, click **Create**.

        When you create a cluster, it takes about half an hour for the cluster status to change from **Starting** to **Running**. If the cluster status does not change after a long period of time,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).

        **Note:** After you activate Realtime Compute for Apache Flink in exclusive mode, Realtime Compute for Apache Flink creates a security group in your VPC and applies for an elastic network interface \(ENI\). For more information, see [ENI overview](/intl.en-US/Network/Elastic Network Interfaces/ENI overview.md). Do not delete the security group or ENI.

3.  Create a project.

    1.  Choose **Cluster Management** \> **Clusters**. Find the cluster for which you want to create a project and click **Create Project** in the **Actions** column.

    2.  In the **Create Project** dialog box, configure**Project Name**, **Project Description**, and **Specified CUs**.

    3.  Click **OK**.


