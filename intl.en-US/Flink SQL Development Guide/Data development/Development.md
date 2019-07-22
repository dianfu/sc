# Development {#concept_62479_zh .concept}

This topic describes SQL code assistance, SQL version management, engine version change, and data storage management during the development phase of Realtime Compute.

Users of Realtime Compute mainly use Flink SQL for data development. For more information, see [Flink SQL](intl.en-US/Flink SQL Development Guide/Flink SQL/Flink SQL overview.md#). In the data development phase, Realtime Compute provides a complete set of integrated development environment \(IDE\) tools, and offers the following functions to assist you with your business development.

## SQL code assistance {#section_ljd_bgs_2gb .section}

-   Flink SQL syntax check

    Realtime Compute supports AutoSave after you modify any text in the IDE. The saving operation triggers SQL syntax check. If a syntax error is detected, the IDE interface displays the row and column where the error is located, and the description of the error.

-   Flink SQL intelligent code completion

    When you enter SQL statements on the Development page of Realtime Compute, the IDE provides the auto-suggestions of keywords, built-in functions, tables, and fields.

-   Flink SQL syntax highlighting

    Flink SQL keywords are highlighted in different colors to differentiate data structures.


## SQL code version management {#section_bbs_bgs_2gb .section}

On this platform, you can manage SQL code versions. A code version is generated each time a user publishes an SQL file for a job. The code management feature allows you to track code changes and roll back to an earlier version if necessary.

-   Version management

    A snapshot of a code version is created after you submit an SQL file for publishing a job every time. This allows you to track code changes. Choose **Development** \> **Versions**. You can view all version information of this job. You can use the comparison feature to view the differences between the new code and the code of the specified version. You can also use the rollback feature to roll back to a specific version.

-   Version clearance

    Realtime Compute has a limit on the maximum number of versions. The default limit of the maximum number of versions supported by Alibaba Cloud is 20. For more information about the limits in other environments, contact your Realtime Compute administrator. If the maximum number of job versions is exceeded, the system does not allow you to submit new versions of the job, and suggests you delete some earlier versions. You can delete some versions from **Versions** on the right sidebar of the Development Platform.

    **Note:** You can submit the job again after the number of job versions is less than or equal to the limit.


## Engine version change {#section_zg2_cgs_2gb .section}

Realtime Compute supports multiple engine versions, and you can choose the right version that suits your business needs. To change the engine version, follow these steps:

-   Upgrade
    1.  In the lower-right corner of the page, click **Recommended Version**.
    2.  Click **OK**.
-   Downgrade
    1.  In the lower-right corner of the page, click **Downgrade**.
    2.  Click **OK**.

        **Note:** You can only change the engine version when your Realtime Compute job is terminated.


## Data storage management {#section_o54_cgs_2gb .section}

The Realtime Compute development platform provides a complete set of convenient tools for data storage management. You can register your data storage resources on the platform to use these tools.

-   Data preview

    The Realtime Compute development platform allows you to preview the data from multiple data storage systems. Data preview helps you efficiently analyze the input and output data, identify key business logic, and complete development tasks.

-   Auto DDL generation

    In most cases, the DDL statements for data storage systems are manually translated into the DDL statements for stream processing. Therefore, the DDL generation process includes a large number of repetitive tasks. Realtime Compute provides an auto DDL generation feature. This feature simplifies the way that you edit SQL statements for stream processing jobs, and reduces the errors when you manually enter SQL statements.


