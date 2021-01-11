# Register a Log Service project

This topic describes how to use Realtime Compute for Apache Flink to register a Log Service project. This topic also provides answers to commonly asked questions about the registration process.

## Introduction to Log Service

Log Service is an end-to-end logging service. Log Service allows you to collect, consume, ship, query, and analyze log data in a quick way. It improves the operations and maintenance \(O&M\) efficiency, and provides the capability to process large amounts of data. Log Service is used to store streaming data. Therefore, Realtime Compute for Apache Flink can use the streaming data that is stored in Log Service as input data.

## Register storage resources

**Note:** Before you use Realtime Compute for Apache Flink to register storage resources, you must grant Realtime Compute for Apache Flink the permission to access these resources. For more information, see [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md).

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane of the Development page, click **Storage**.
4.  In the upper-left corner of the Storage page, click **+Registration and Connection**.
5.  In the **Register Data Store and Test Connection** dialog box, configure the storage parameters.
6.  Click **OK**.

## Parameters

-   Endpoint

    The endpoint of the Log Service project that you want to register. The endpoint of a Log Service project varies based on the region of this project. For more information, see [Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).

    **Note:**

    -   The endpoint of a Log Service project must start with `http://` and cannot end with a forward slash \(`/`\). For example, the endpoint can be `http://cn-hangzhou-intranet.log.aliyuncs.com`.
    -   Realtime Compute for Apache Flink and Log Service are deployed in the internal network of Alibaba Cloud. We recommend that you enter the endpoint of the classic network or the virtual private cloud \(VPC\) for the project. We recommend that you do not enter the public endpoint. If Realtime Compute for Apache Flink accesses a Log Service project over the Internet by using the public endpoint, the system may consume the resources of Internet bandwidth. In this case, the system performance may be compromised.
-   Project

    The name of the Log Service project that you want to register.

    **Note:** In Realtime Compute for Apache Flink, you can register only the Log Service projects that are owned by the current Alibaba Cloud account. Assume that User A owns Project A of Log Service. If User B needs to use the storage resources of Project A in Realtime Compute for Apache Flink, this system does not allow User B to register Project A. If you need to use the Log Service project that is owned by another Alibaba Cloud account, you can use the plaintext mode to use the project. For more information, see [Use a plaintext AccessKey pair](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Overview.md).


## FAQ

What do I do if I fail to register a storage resource in Realtime Compute for Apache Flink?

Realtime Compute for Apache Flink uses a storage software development kit \(SDK\) to access different data storage systems. The Storage tab on the Realtime Compute for Apache Flink development platform helps you manage data from different data storage systems. If you fail to register a storage resource in Realtime Compute for Apache Flink, troubleshoot the issue by performing the following steps:

-   Check whether the Log Service project is created in your Alibaba Cloud account. Log on to the [Log Service console](https://sls.console.aliyun.com/) and check whether you can access the project.
-   Check whether the Log Service project is owned by your Alibaba Cloud account. You cannot register a project that is owned by another Alibaba Cloud account.
-   Check whether the endpoint and the name of the Log Service project are valid. The endpoint of the Log Service project must start with `http://` and cannot end with a forward slash \(`/`\).
-   Check whether the endpoint of the Log Service project is the classic network endpoint. Realtime Compute for Apache Flink does not support VPC endpoints.
-   Check whether you have already registered the Log Service project. Realtime Compute for Apache Flink provides a registration check mechanism to prevent duplicate registrations.

Why does Realtime Compute for Apache Flink support only time-based data sampling?

Log Service stores streaming data and supports only time-based data sampling. You can specify only time parameters in the Log Service API. Therefore, Realtime Compute for Apache Flink supports only time-based data sampling.

