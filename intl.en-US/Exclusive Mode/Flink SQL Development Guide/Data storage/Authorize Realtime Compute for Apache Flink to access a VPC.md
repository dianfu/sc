# Authorize Realtime Compute for Apache Flink to access a VPC

To allow Realtime Compute for Apache Flink in shared mode to access storage resources in a virtual private cloud \(VPC\), you must grant Realtime Compute for Apache Flink this permission. This topic describes how to grant Realtime Compute for Apache Flink the permission to access a VPC.

## Background information

Realtime Compute for Apache Flink projects in shared mode are deployed in the classic network. To allow Realtime Compute for Apache Flink to access storage resources in a VPC, you must grant the system the permission to access the VPC. Realtime Compute for Apache Flink can access only ApsaraDB for RDS instances in a VPC.

**Note:**

-   Realtime Compute for Apache Flink clusters in exclusive mode can access the VPCs without authorization because these clusters reside in VPCs.
-   After you grant Realtime Compute for Apache Flink in shared mode the permission to access a VPC, some performance issues may occur when the system accesses storage resources in the VPC. For example, the bandwidth may be restricted. To avoid such issues, we recommend that you do not access storage resources in a VPC from Realtime Compute for Apache Flink in shared mode.
-   As of December 24, 2019, Realtime Compute for Apache Flink in shared mode is no longer available. You cannot purchase projects in this mode. You can only scale out, scale in, or renew existing shared-mode projects. We recommend that you purchase the exclusive mode or the Flink cloud-native mode of Realtime Compute for Apache Flink based on your business requirements.

## Procedure

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  Move the pointer over the username in the upper-right corner.
3.  In the list that appears, click **Project Management**.
4.  In the left-side navigation pane, click **VPC Access Authorization**.
5.  In the upper-right corner of the VPC Access Authorization page, click **Add Authorization**.
6.  In the **Authorize StreamCompute VPC Access** dialog box, specify the parameters. The following table describes these parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Name**|The name of the VPC.|
    |**Region**|The region where the storage resource resides.|
    |**VPC ID**|The ID of the VPC. To view the VPC ID of an ApsaraDB for RDS instance, perform the following steps:     1.  Log on to the [ApsaraDB for RDS console](https://rds.console.aliyun.com/).
    2.  In the top navigation bar, select the region where the ApsaraDB for RDS instance resides.
    3.  Find the ApsaraDB for RDS instance and then click the instance ID.
    4.  In the left-side navigation pane, click **Database Connection**.
    5.  Navigate to **Instance Connection** \> **Database Connection** \> **Network Type** to view the VPC ID, such as `vpc-bp1lysht98wrvl9n3****`. |
    |**Instance ID**|The instance ID of the storage resource in the VPC. To view the ID of an ApsaraDB for RDS instance, perform the following steps:     1.  Log on to the [ApsaraDB for RDS console](https://rds.console.aliyun.com/).
    2.  In the top navigation bar, select the region where the ApsaraDB for RDS instance resides.
    3.  Find the ApsaraDB for RDS instance and click the instance ID to go to the Basic Information page.
    4.  Choose **Basic Information** \> **Instance ID** to view the instance ID. |
    |**Instance Port**|The port of the storage resource in the VPC. For more information about how to view the port of an ApsaraDB for RDS instance, see [View and change the internal and public endpoints and port numbers of an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Database connection/View and change the internal and public endpoints and port numbers of an ApsaraDB
         RDS for MySQL instance.md).|


## FAQ

How do I configure the url parameter when I use a DDL statement to reference a storage resource in a VPC?

When you use data definition language \(DDL\) statements to reference storage resources in a VPC, you can set the url parameter in the WITH clause based on the **Mapping IP Address** and **Mapping Port** parameters on the VPC Access Authorization page. For example, you can set the url parameter to url='jdbc:mysql://<mappingIP\>:<mappingPort\>/<databaseName\>'. To obtain the values of the **Mapping IP Address** and **Mapping Port** parameters, perform the following steps:

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  Move the pointer over the username in the upper-right corner.
3.  In the list that appears, click **Project Management**.
4.  In the left-side navigation pane, click **VPC Access Authorization**.
5.  On the VPC Access Authorization page, view the values of the **Mapping IP Address** and **Mapping Port** parameters.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5221659951/p36284.png)


