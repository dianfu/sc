# Product security

Realtime Compute for Apache Flink ensures account, business, and data security for end-to-end real-time computing.

Account security involves Realtime Compute for Apache Flink and data stores.

-   Account security of Realtime Compute for Apache Flink

    Only Alibaba Cloud accounts can be used as Realtime Compute for Apache Flink accounts. The account information includes a username and its password or a username and a signature key. HTTPS is used to secure account information. For more information about account security of Realtime Compute for Apache Flink, see [Alibaba Cloud account and RAM user authorization](/intl.en-US/Exclusive Mode/Preparation/Alibaba Cloud account and RAM user authorization.md).

-   Account security of data stores

    In Realtime Compute for Apache Flink, the accounts of data stores are used to create source and result tables. Realtime Compute for Apache Flink provides Resource Access Management \(RAM\) and Security Token Service \(STS\) to prevent the leakage of your business data due to the loss of account information. For more information about account security for data stores, see [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md).


## Business security

Business security of Realtime Compute for Apache Flink is used to isolate projects and secure business processes.

-   Project isolation

    Realtime Compute for Apache Flink projects are isolated based on project permissions. Only users that belong to a project can access or manage authorized sub-product entities in the project. Project-level resource isolation ensures that other users do not interfere with your operations.

    **Note:** For example, if the data amount increases dramatically when a job of a user is running, the CPU utilization of the job is increased. Due to resource isolation, the CPU utilization of jobs of other users is not affected.

-   Business process

    Realtime Compute for Apache Flink provides separate pages for data development and administration to clearly demonstrate the entire development process of stream processing in its console. This guarantees a complete and secure business process.

    -   Code version

        Realtime Compute for Apache Flink allows you to compare code versions and roll back to an earlier version. This helps you trace the code and rectify faults.

    -   Standalone debugging tool in the IDE

        Realtime Compute for Apache Flink offers a debugging tool in the integrated development environment \(IDE\), which allows you to debug the code without affecting online data. You can specify data for source tables, dimension tables, and result tables to create a job, and then debug the data offline. This ensures that running jobs are not affected.

    -   Job publishing process

        The job publishing process is secure. You can debug the code without affecting online data. After you debug the new code, you can publish the job and view it on the Administration page of the Realtime Compute for Apache Flink development platform. Realtime Compute for Apache Flink jobs that are running do not directly use the new code. To use the new code, you must stop the jobs and then restart them with the new code. .


## Data security

Realtime Compute for Apache Flink ensures the security of its system data and business data.

-   System data security

    Realtime Compute for Apache Flink ensures its data security in the following aspects:

    -   HTTPS is used to secure transmission links.
    -   The Advanced Encryption Standard \(AES\) is used to encrypt information about the connections with data stores. This helps prevent the leakage of sensitive information.
    -   Realtime Compute for Apache Flink has passed comprehensive and in-depth attack tests.
    -   Alibaba Cloud security team provides security services for Realtime Compute for Apache Flink.
-   Business data

    Realtime Compute for Apache Flink does not store the business data of users. The security of business data is ensured by Alibaba Cloud storage systems. For more information, see the security models and best security practices of Alibaba Cloud storage systems.


