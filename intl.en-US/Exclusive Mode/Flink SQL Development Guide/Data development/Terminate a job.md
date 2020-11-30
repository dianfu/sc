---
keyword: [terminate, make changes take effect]
---

# Terminate a job

After you modify the SQL logic, change the job version, add parameters to the WITH clause, or add job parameters for a job, you must terminate and then start the job to make the changes take effect. This topic describes how to terminate a job.

**Note:**

-   You can only **terminate** a job that is in the **Running** or **Starting** state.
-   If you **terminate** a job, its task status is cleared. For example, if the job you **terminate** is running a COUNT operation, the COUNT operation starts from 0 after you **start** the job.
-   The **Terminate \(checkpoint\)** operation is supported in Realtime Compute for Apache Flink V3.5.0 and later. If your Realtime Compute for Apache Flink is earlier than V3.5.0, the following error message is displayed when you try to perform this operation: **An error occurred. System error: The BLINK version is abnormal. Error reason: blink version \>= blink-3.5 is required, instance blink-3.4.4**.

To terminate a job, perform the following steps:

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  On the **Administration** page, find the job that you want to terminate, and click **Terminate** in the **Actions** column.

    **Note:** The **Terminate \(checkpoint\)** operation under **More** is different from the **Terminate** operation. The system triggers a checkpoint when you perform the **Terminate \(checkpoint\)** operation to terminate a job. Therefore, the time consumed to terminate a job by performing the **Terminate \(checkpoint\)** operation is longer than that by performing the **Terminate** operation. The job status is cleared after the job is terminated. The Terminate \(checkpoint\) operation has other functions in some scenarios. For example, if the upstream storage system is Message Queue for Apache Kafka, the system submits an offset each time it triggers a checkpoint. This ensures that the number of offsets submitted to the Kafka server is consistent with the amount of data consumed.


