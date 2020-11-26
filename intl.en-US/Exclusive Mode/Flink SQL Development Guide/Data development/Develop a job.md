---
keyword: [development process, Syntax check, Flink SQL code assistance, management of Flink SQL code versions, project parameters, job parameters]
---

# Develop a job

This topic describes how to create a Realtime Compute for Apache Flink job. This topic also describes the features provided on the Development page, such as syntax check, Flink SQL code assistance, and management of Flink SQL code versions.

**Note:**

## Write the Flink SQL code of a job

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, click **Development**.

3.  At the top of the **Development** page, click **Create File**

4.  In the **Create File** dialog box, specify the parameters. The following table describes these parameters.

    |Parameter|Description|
    |---------|-----------|
    |**File Name**|The name of the file that contains the Fink SQL code. **Note:** The file name must be unique in the existing project. |
    |**File Type**|    -   For Realtime Compute for Apache Flink in shared mode, the valid value is only **FLINK\_STREAM/SQL**.
    -   For Realtime Compute for Apache Flink in exclusive mode, valid values are **FLINK\_STREAM/DATASTREAM** and **FLINK\_STREAM/SQL**. |
    |**Storage Path**|The folder of the file that contains the Fink SQL code. On the right side of the existing folder, you can also click the ![Create a folder](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2627736061/p129608.png) icon to create a subfolder.|

5.  Click **OK**.

6.  On the page that appears, write the Flink SQL code for the job.

    **Note:**

    -   On the right side of the **Development** page, you can click **Code Structure** to check the Flink SQL code structure.
    -   On the left side of the **Development** page, we recommend you click **Storage** to manage upstream and downstream storage resources. For more information, see [Overview](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Overview.md).

## Specify job parameters

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, click **Development**.

3.  In the left-side navigation pane of the **Development** page, click the job name.

4.  On the right side of the **Development** page for the job, click **Parameters**.

5.  Specify the parameters required for the job.

    For more information about job parameters, see [Job parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).


## Specify project parameters

The job parameters are valid for a single job. The project parameters are valid for all the jobs in the project. After the project parameters are configured, the following two operations are performed:

-   Replacing variables: After you click Start, Debug, or Syntax Check, the system replaces the variables in SQL jobs or in the code of the jobs that are created by using the Flink DataStream API.
-   Distributing parameters: The project-level system parameters are merged with job parameters and startup parameters. The startup parameters can be configured for only batch jobs. The following parameters are sorted in descending order based on their priorities: startup parameters \> job parameters \> project-level system parameters. The merged parameters are used as final parameters and are distributed to the Blink job. For example, if job parameters and project parameters conflict, the job parameters prevail.

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, move the pointer over your profile picture. In the list that appears, click **Project Management**.

3.  In the **Project Name** column of the **Projects** page, click the name of the project for which you want to configure parameters.

4.  In the top navigation bar, click **Development**.

5.  On the left side of the **Development** page, click the job for which you want to configure the parameters.

6.  Enable project parameter configuration.

    By default, `disable.project.config=false` is specified. This indicates that you cannot configure project parameters. You can enable project parameter configuration by using the following methods:

    -   SQL jobs: On the right side of the Development page, click **Parameters** and specify the following setting: `enable.project.config=true`.
    -   Flink DataStream job: In the job code, specify `enable.project.config=true parameter`.
7.  At the top of the Development page, click **Project Parameter**.

8.  Configure the project parameters

    Project parameters support only two job types: SQL jobs and Flink DataStream jobs. When you configure project system parameters, you must add the job type to the beginning of the project parameters. For example, you can use `sql.name=LiLei` or `datastream.name=HanMeimei`.


## Enable syntax check

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, click **Development**.

3.  On the left side of the **Development** page, click the job for which you want to check syntax.

4.  At the top of the **Development** page, click **Syntax Check**.

    **Note:**

    -   When you save a Flink SQL job, the system automatically checks the syntax of this job.
    -   **Syntax Check** takes effect for only Flink SQL statements that have complete logic. Otherwise, **Syntax Check** does not take effect.

## Flink SQL code assistance

-   Syntax check

    After you modify the Flink SQL code, the system automatically saves the code and checks the syntax. If a syntax error is detected, the system displays the cause of the error, the row and column where the error occurred on the **Development** page.

-   Intelligent code completion

    When you enter Flink SQL statements on the **Development** page, the system automatically performs intelligent code completion, including keywords, built-in functions, tables, and fields.

-   Syntax highlighting

    The system highlights keywords in Flink SQL statements and displays different structures in different colors.


## Management of Flink SQL code versions

Realtime Compute for Apache Flink allows you to manage Flink SQL code versions. A new code version is generated each time you publish a job. You can use code versions to track versions, modify the code, and roll the code back to an earlier version.

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, click **Development**.

3.  On the left side of the **Development** page, click the job for which you want to manage the code version.

4.  On the right side of the **Development** page, click **Versions**.

5.  In the Versions pane, find the code version of the job and choose **Actions** \> **More**.

6.  Select one of the following options from the drop-down list:

    -   **Compare**: checks the difference between the current version and an earlier version.
    -   **Rollback**: rolls the code back to an earlier version.
    -   **Delete**: deletes a code version. By default, you can reserve a maximum of 20 Flink SQL code versions in Realtime Compute for Apache Flink. If the number of code versions is less than 20, you can publish a job. If the number of code versions is 20, the system does not allow you to publish a job and prompts you to delete earlier versions.

        **Note:** You can publish a job only if the number of versions is less than 20.

    -   **Locked**: locks the current version.

        **Note:** You can submit a new version only after you unlock the current version.


