# Register Table Store resources {#concept_62477_zh .concept}

This topic describes how to connect to Table Store by registering Table Store resources in Realtime Compute.

## Register Table Store resources {#section_wff_snb_yfb .section}

Table Store is a NoSQL data storage service built on the Apsara system of Alibaba Cloud. It enables you to store and access large amounts of structured data in real time. Realtime Compute has high requirements on access delay, and has low requirements on the relational algebra. Therefore, Table Store dimension tables and result tables are very suitable for Realtime Compute.

**Note:** If you encounter errors that are similar to the `No Permission` error when you use Table Store, grant Realtime Compute the permission to access Table Store. For more information, see [Create a role for Realtime Compute exclusive mode and grant permissions to the role](../../../../intl.en-US/Preparation/Create a role for Realtime Compute exclusive mode and grant permissions to the role.md#).

-   Endpoint

    -   Enter the endpoint of Table Store. View the endpoint information of Table Store in the [Table Store console](https://ots.console.aliyun.com), and enter the VPC address of Table Store. For more information, see [Endpoint](../../../../intl.en-US//Endpoint.md#).
    -   Set Accessed By to **Any Network**. Log on to the [Table Store console](https://ots.console.aliyun.com/). Choose **Instance Details** \> **Network** \> **Change** \> **Any Network**.
-   Instance name

    Enter the name of the Table Store instance.


