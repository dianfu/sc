# Performance optimization by using auto scaling

Realtime Compute earlier than V3.0.0 provides AutoConf for you to improve job performance. However, AutoConf requires you to frequently restart the job. Realtime Compute V3.0.0 and later support auto scaling to resolve this issue. After you start a job, Realtime Compute adjusts the job configuration to reach the preset performance goal based on resource configuration rules. This process does not require any manual operations.

**Note:**

-   Auto scaling is only supported in Realtime Compute V3.0.0 and later.
-   Before you upgrade Realtime Compute to V3.0.0, delete all PlanJSON files generated in Realtime Compute earlier than V3.0.0 and reacquire configuration files.

## Enable auto scaling

You can enable auto scaling when you publish a job.

1.  Go to the job editing page on the Realtime Compute development platform.
    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
    2.  In the top navigation bar, click **Development**.
    3.  On the **Development** page that appears, double-click the target job, which may be nested under a folder, to go to the job development page.
2.  On the top of the job editing section, click **Publish** to go to the Publish New Version page.
3.  In the **Initial Resources** step, select a resource type and click **Next**.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3421659951/p46751.png)

    -   **Use last time Auto Scaling**: uses the PlanJSON file for the latest auto scaling to start the job. You can select **Use Latest Auto Scaling Configurations** when the following conditions are met:
        -   The job is published with auto scaling enabled and adopts the latest configuration.
        -   The job is in the **suspended** state.
        -   You have obtained the auto scaling configuration. To do so, click **Configurations** on the right, move the pointer to Configurations in the upper-right corner, and select **Acquire Auto Scaling Configuration**.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3421659951/p59274.jpg)

    -   **Default**: uses the default resource configuration to start the job. You can select this option to publish a new job or an existing job whose logic is not modified and compatible with Realtime Compute.
    -   **Manual**: uses manually configured resource configuration to start the job. Select this option to manually configure resources or modify the auto scaling configuration.
4.  In the **Check** step, check the job and click **Next**.
5.  In the **Resource Configuration** step, configure **auto scaling** parameters and click **Next**.

    |Parameter|Description|
    |---------|-----------|
    |**Auto Scaling**|Specifies whether to enable auto scaling. Select **ON**.|
    |**Maximum number of PlanJson CUs**|The maximum available CUs for the job. One CU consists of one CPU core and 4 GB of memory. The value of this parameter must be less than the number of available CUs in the project.|
    |**Optimization Policy**|The policy for optimizing the job configuration. Valid value: **Data Pending Time**. Realtime Compute optimizes the job configuration based on the **Optimization Policy** and **Expected Value**.|
    |**Expected Value**|The threshold of the data pending time, in seconds. If data from the data source has been pending for a period of time that exceeds the threshold, Realtime Compute triggers auto scaling to adjust the parallelism for the job.|

    **Note:** For example, set Expected Value to 5. If data from the data source has been pending for more than 5 seconds, Realtime Compute keeps reducing the parallelism for the job until the pending time is shortened to less than 5 seconds.

6.  In the **Publish File** step, click **Publish**.
7.  Start the job. For more information, see [Start a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Start a job.md).

## Disable auto scaling

**Note:** You can disable auto scaling for a job only when the job is published with auto scaling enabled.

You can disable auto scaling for a job in the running state.

1.  Go to the **Job Administration** page on the Realtime Compute development platform.
    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
    2.  In the top navigation bar, click **Administration**.
    3.  On the **Jobs** page that appears, click the target job name under the **Job Name** field.
2.  Click **Disable Auto Scaling** in the upper-right corner.
3.  Click **OK**.

**Note:** The actions in the **Auto Configuration** column on the **Job Administration** page are available only if auto scaling is enabled when you publish the job.

## View information about auto scaling

**Note:** Go to the **Job Administration** page. Follows these steps:

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  On the **Jobs** page that appears, click the target job name under the **Job Name** field.

-   Auto scaling metrics

    Navigate to **Curve Charts** \> **Overview** to view information about auto scaling.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3421659951/p46778.png)

    |Metric|Description|
    |------|-----------|
    |**The number of successful auto scaling and the number of failure**|The number of successful and failed auto scaling operations|
    |**CPUs consumed during auto scaling**|The CPU resources used for auto scaling|
    |**Memory consumed during auto scaling**|The memory resources used for auto scaling|

-   Information about the PlanJSON file generated for auto scaling

    On the Job Administration page, navigate to **Properties and Parameters** \> **Resource Configuration** \> **Plans**. Select the target version to view information about the PlanJSON file generated for auto scaling.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3421659951/p46779.png)


## FAQ

-   Q: What do I do if auto scaling cannot be triggered?

    A: Follow these steps to troubleshoot the issue:

    -   Check whether the job fails frequently. To trigger auto scaling, make sure that the job is in correct logic and runs stably.
    -   View **JobManager** logs to check whether system exceptions occur.
-   Q: What do I do if auto scaling does not take effect?

    A: Follow these steps to troubleshoot the issue:

    1.  Check whether the resources consumed by the job reach the upper limit.
    2.  Check the source node logic to find whether too many operators are connected to the source node. If too many operators are connected to the source node, edit the PlanJSON file to remove some operators. For more information, see [Optimize performance by manual configuration](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).
    3.  View **JobManager** logs to check whether system exceptions occur.
-   Q: What problems might I run into if I enable auto scaling?

    A: You might encounter the following problems:

    -   The job is automatically restarted.

        If you enable auto scaling, Realtime Compute adjusts the parallelism and resources based on the volume of data streams. It may automatically restart the job to adjust resources when the data streams increase or decrease.

    -   Data transmission is delayed within a short period of time.

        When the data streams enter the off-peak period, Realtime Compute triggers auto scaling to reduce the parallelism and resources. When the data streams increase, the resources may become insufficient for data transmission, which causes delay within a short period of time.

    -   The job cannot be resumed.

        In some scenarios, auto scaling does not function well, and the job may be delayed. Realtime Compute has to frequently adjust the job configuration. As a result, the job cannot be resumed.

    -   The parallelism is reduced and then increased.

        For a job that uses window functions or aggregate functions, when the state data increases, the performance for accessing the state data degrades, and the parallelism is reduced when the job is started. When the job is running, the parallelism increases as the state data accumulates until the volume of the state data becomes steady.


