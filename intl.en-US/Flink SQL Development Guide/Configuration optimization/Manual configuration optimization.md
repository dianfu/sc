# Manual configuration optimization {#concept_qc2_khm_bgb .concept}

This topic describes how to manually optimize the configuration of a Realtime Compute job.

## Manual configuration optimization {#section_q1g_lhm_bgb .section}

You can manually optimize the configuration of a Realtime Compute job by using one or all of the following methods:

-   Fine-tune job parameters such as miniBatch.
-   Fine-tune resource parameters, such as parallelism, core, and heap\_memory, for operators.
-   Fine-tune input and output storage parameters of the job.

More details about these three methods are described in the following sections. After you fine-tune the specific parameters, Realtime Compute generates a new configuration. You need to republish the job or resume the job \(if it is suspended\) to apply the new configuration. The detailed process is provided in the last section of this topic.

## Fine-tune job parameters {#section_sxj_yjm_bgb .section}

The miniBatch parameter can be used to optimize only GROUP BY operators. During the streaming data processing of Flink SQL, the state is read each time a data record arrives for processing, which consumes large amounts of I/O resources. After you set the miniBatch parameter, Realtime Compute reads the state only once for data records with the same key, and the output contains only the latest data record. This reduces the frequency of reading the state and minimizes the data output updates. When you add miniBatch as a new parameter for your job, we recommend that you terminate the job before you set the parameter, and then restart the job. If you want to change the value of this parameter, you can suspend the job beforehand, and then resume the job.

``` {#codeblock_y7k_vd2_9rv}
# excatly-once semantics
blink.checkpoint.mode=EXACTLY_ONCE
# The checkpoint interval, in milliseconds.
blink.checkpoint.interval.ms=180000
blink.checkpoint.timeout.ms=600000
# Realtime Compute V2.X uses Niagara as the state back-end, and uses it to set the lifecycle of the state data, in milliseconds.
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000
# Realtime Compute V2.X enables a 5-second micro-batch (You cannot set this parameter when you use a window function.)
blink.microBatch.allowLatencyMs=5000
# The allowed latency for a job.
blink.miniBatch.allowLatencyMs=5000
# The size of a batch.
blink.miniBatch.size=20000
# Local optimization. This feature is enabled by default in Realtime Compute V2.X, but you need to enable it manually if you use Realtime Compute V1.6.4.
blink.localAgg.enabled=true
# Realtime Compute V2.X allows you to enable PartialFinal to solve data hotspot problems when you run the CountDistinct function.
blink.partialAgg.enabled=true
# union all optimization
blink.forbid.unionall.as.breakpoint.in.subsection.optimization=true
# GC optimization (You cannot set this parameter when you use a Log Service source table.)
blink.job.option=-yD heartbeat.timeout=180000 -yD env.java.opts='-verbose:gc -XX:NewRatio=3 -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:ParallelGCThreads=4'
# Time zone setting
blink.job.timeZone=Asia/Shanghai
			
```

## Fine-tune resource parameters {#section_dj3_clm_bgb .section}

1.  Problem analysis
    1.  As shown in the following topology, the percentage of input queues at task node 2 has reached 100%. The data of task node 2 is stacked up and puts pressure back on task node 1, at which the percentage of output queues has reached 100%.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41064/156583953133889_en-US.png)

    2.  You can click task node 2 and locate the subtask in **SubTask List**where the percentage of **InQueue** has reached 100%. Then, click **View Logs** to view the detailed information.
    3.  Check the CPU and memory usage in **TaskExecutor** \> **Metrics Graph**. Increase the CPU capacity and memory size based on the actual use.
2.  Performance optimization

    1.  On Development , click **Basic Properties** \> **Configure Resources** .
    2.  Locate the group \(if any\) or operator that corresponds to task node 2. You can modify the parameters of one or multiple operators in one group at a time.
        -   Modify parameters of multiple operators in a group:
            1.  Hover your mouse on the target GROUP box.
            2.  Click the **Pencil** icon .
            3.  Modify Operator parameters in **Modify Operator Data**.
        -   Modify parameters of a single operator:
            1.  Click**➕** at the group box where the target operator belongs to.
            2.  Hover your mouse on the target operator box.
            3.  Click the **Pencil** icon .
            4.  Modify Operator parameters in **Modify Operator Data**.
    3.  After you modify the parameters, click **Apply and Close** in the upper-right corner of the page.
    **Note:** 

    During optimization, if the resource configuration of a group has been optimized but the performance still does not improve, you need to verify whether data skew exists in the current node. If data skew is detected, fix it immediately. Then, separate operators that involve complex computation \(such as GROUP BY, WINDOW, and JOIN\) from the group, and locate the abnormal operator. Fine-tune the abnormal operator. To separate an operator from a group, click the operator to be modified and change the value of its chainingStrategy parameter to HEAD. If the value is already HEAD, click the next operator and modify the value of its chainingStrategy parameter to HEAD. The options for the chainingStrategy parameter are as follows:

    -   ALWAYS: combines the operator with others to form a group.
    -   NEVER: retains the status of the operator.
    -   HEAD: separates the operator from a group.
3.  Principles and recommendations
    -   Adjustable parameters
        -   parallelism
            -   Sources

                **Note:** The number of parallel subtasks in the source must not be greater than the number of shards in the source table.

                1.  Set the parallelism parameter based on the number of source table partitions.
                2.  For example, if the number of source table partitions is 16, set the parallelism parameter to 16, 8, or 4. Note that the maximum value is 16.
            -   Intermediate processing nodes
                1.  Set the parallelism parameter based on the estimated queries per second \(QPS\).
                2.  For tasks with low QPS, set the parallelism parameter for the intermediate processing nodes to the same value as that for the sources.
                3.  For tasks with high QPS, set the parallelism parameter to a larger value, such as 64, 128, or 256.
            -   Sinks
                1.  Set the parallelism parameter for sinks to a value that is two or three times the number of result table partitions.
                2.  However, if the specified parallelism limit is exceeded, a write timeout or failure occurs. For example, if the number of output sinks is 16, the recommended maximum value of the parallelism parameter for sinks is 48.
        -   core

            This parameter indicates the number of CPU cores. The default value is 0.1. Set this parameter based on CPU usage. We recommend that you set this parameter to a value whose reciprocal is an integer. The recommended value is 0.25.

        -   heap\_memory

            This parameter indicates the heap memory size, whose default value is 256 MB. The value is determined based on the actual memory usage. You can click GROUP on the resource editing page to modify the preceding parameters.

    -   Adjustable parameters at task nodes with GROUP BY operators

        state\_size: specifies the state size. The default value is 0. If the operator state is used, set the state\_size parameter to `1`. In this case, the corresponding job requests extra memory for this operator. The extra memory is used to store the state. If the state\_size parameter is not set to `1`, the corresponding job may be killed by YARN. Operators whose state\_size needs to be set to 1: GROUP BY, JOIN, OVER, and WINDOW. In the face of so many configuration items, you can focus on these parameters: core, parallelism, and heap\_memory. For each job, we recommend that you assign 4 GB memory for each core.``

        **Note:** 

        Rules to follow when you adjust the parallelism and memory size:

        Total number of compute units \(CUs\) of an operator = Value of parallelism × Number of cores. Total memory size of an operator = Value of parallelism × heap\_mem. The CPU to MEM ratio must be 1:4, where CPU is the maximum number of CPU cores within a group, and MEM is the total memory of each operator within the group. For example, if you have 1 CU and 3 GB memory, the final configuration would be 1 CU and 4 GB memory. If you have 1 CU and 5 GB memory, the final configuration would be 1.25 CUs and 5 GB memory.


## Fine-tune input and output storage parameters {#section_wml_snm_bgb .section}

Realtime Compute enables real-time computing, which means that each data record can trigger read and write operations on source and result tables. This brings considerable challenges for data storage performance. To address these challenges, you can set batch size parameters to specify the number of data records that are read from a source table or written into a result table at a time. The following table describes the available batch size parameters.

|Table|Parameter|Description|Value|
|DataHub source table|batchReadSize|The number of data records that are read at a time.|Optional. Default value: 10.|
|DataHub result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 300.|
|Log Service source table|batchGetSize|The number of log groups that are read at a time.|Optional. Default value: 10.|
|AnalyticDB result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 1000.|
|ApsaraDB for RDS result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 50.|
|HybridDB for MySQL result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 1000. We recommend that you set a value smaller than 4096.|
|bufferSize|The buffer size after deduplication. This parameter takes effect only when the primary key is specified.|Optional. You must set bufferSize before you can set batchSize. We recommend that you set bufferSize to a value smaller than 4096.|

## Apply the new configuration {#section_isd_5nm_bgb .section}

After completing parameter settings in the preceding sections, you need to restart your job or resume your job \(if it is suspended\) to apply the new configuration.

1.  Republish the job. In the Publish New Version dialog box, select **Use Latest Manually Configured Resources** for **Resource Configuration**.
2.  Suspend the job.
3.  Resume the job.
4.  In the Resume Job dialog box, select **Resume with Latest Configuration**. Otherwise, the new configuration cannot take effect.
5.  After you resume the job, you can choose **Administration** \> **Overview** \> **Vertex Topology** to check whether the new configuration has taken effect.

**Note:** 

We do not recommend that you terminate and restart a job to apply the new configuration. After a job is terminated, its status is cleared. In this case, the computing result may be inconsistent with the result that is obtained if you suspend and resume the job.

## Glossary {#section_s12_c4m_bgb .section}

-   global
    -   isChainingEnabled: indicates whether chaining is enabled. Default value: true. **Use the default value**.
-   nodes
    -   id: specifies the unique ID of a node. The ID is automatically generated and **does not need to be changed**.
    -   uid: specifies the UID of a node, which is used to calculate the operator ID. If this parameter is not specified, the ID is used.
    -   pact: specifies the type of a node. Example values: Data Source, Operator, and Data Sink. **Use the default value**.
    -   name: specifies the name of a node, which can be customized.
    -   slotSharingGroup: `default`. **Use the default value**.
    -   chainingStrategy: specifies the chaining strategy. Valid values: HEAD, ALWAYS, and NEVER. **You can change the value as needed**.
    -   parallelism: specifies the number of parallel subtasks. Default value: `1`. You can increase the value based on the data volume.
    -   core: specifies the number of CPU cores. Default value: `0.1`. The value is configured based on the CPU usage. We recommend that you set this parameter to a value whose reciprocal is an integer. The recommended value is `0.25`.
    -   heap\_memory: specifies the heap memory size. Default value: 256 MB. Set this parameter based on the memory usage.
    -   direct\_memory: specifies the JVM non-heap memory size. Default value: `0`. Use the default value.
    -   native\_memory: specifies the JVM non-heap memory size for the Java Native Interface \(JNI\). Default value: `0`. The recommended value is 10 MB.
-   chain
    -   A Flink SQL job resembles a Directed Acyclic Graph \(DAG\) that contains many nodes, which are also known as operators. Some input and output operators can be combined to form a chain when they are running. The CPU capacity of a chain is set to the maximum CPU capacity among operators in the chain. The memory size of a chain is set to the total memory size of operators in the chain. For example, node 1 \(256 MB, 0.2 cores\), node 2 \(128 MB, 0.5 cores\), and node 3 \(128 MB, 0.25 cores\) are combined to form a chain. The CPU capacity of the chain is 0.5 cores and the memory is 512 MB. The prerequisite for chaining operators is that the operators to be chained must have the same parallelism settings. However, some operators cannot be chained, such as GROUP BY operators. We recommend that you chain operators to improve the efficiency of network transmission.

