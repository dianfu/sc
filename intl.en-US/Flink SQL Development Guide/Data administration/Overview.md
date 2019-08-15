# Overview {#concept_62482_zh .concept}

The Overview page shows the real-time running status and instantaneous values of a job. Based on the analysis of the job status, you can determine whether the job is healthy, and whether it meets your expectations.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41066/156584754633933_en-US.png)

## Health score {#section_bc3_5dr_bgb .section}

To help you quickly locate job performance issues, Realtime Compute offers a health check feature.

A health score of less than 60 points indicates that the current node has stacked up some data, and its data processing capacity is insufficient. You can solve this issue by using [Manual configuration optimization](reseller.en-US/Flink SQL Development Guide/Configuration optimization/Manual configuration optimization.md#). You can optimize the performance based on your business requirements.

## Task status {#section_ss9_hnu_bd2 .section}

A task can be in any of the following statuses: created, running, failed, completed, scheduled, canceling, and canceled. You can determine whether a job is running properly based on the task status.

## Job instantaneous values {#section_7uk_ywg_ifl .section}

|Name|Description|
|----|-----------|
|Computing duration|Indicates the computing performance of the job.|
|Input TPS|The number of blocks read from the source table every second. If you use Log Service, it can include multiple data records into a LogGroup for Realtime Compute to read. The number of blocks reflects the number of LogGroups that are read by Realtime Compute from the source table every second.|
|Input RPS|The number of data records that are read from the source table every second. The unit is record/s.|
|Output RPS|The number of data records that are written into result tables every second. The unit is record/s.|
|CPU usage|The current CPU usage of the job.|
|Start time|The start time of the job.|
|Running duration|The duration that the job has been running since it was started.|

## Running topology {#section_zyy_eot_eui .section}

A running topology shows how the underlying computational logic of Realtime Compute works. Each component corresponds to a task. Each data stream starts from one or more data sources and ends in one or more result tables. The flow of data streams is similar to a Directed Acyclic Graph \(DAG\). For more efficient distributed execution, Realtime Compute chains operator subtasks together into tasks if possible. Every task is run in a thread. Combining operators into a task reduces inter-thread switching, serialized or deserialized messages, and data exchange in the cache, shortening latency and increasing overall throughput. An operator indicates a computational logic operator, and a task is a collection of multiple operators.

-   View mode

    To help you better understand the abstract underlying computational logic of Realtime Compute, the Realtime Compute platform offers the following view.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41066/156584754633937_en-US.png)

    The detailed information about a task is as follows. When you move the pointer over a task, the detailed information appears.

    |Name|Description|
    |----|-----------|
    |ID|The task ID in the running topology.|
    |PARALLEL|The number of parallel subtasks.|
    |TPS|The amount of data read from the input tables, which are measured in blocks per second.|
    |LATENCY|The computing duration of the task node.|
    |DELAY|The processing delay at the task node.|
    |IN\_Q|The percentage of input queues for the task node.|
    |OUT\_Q|The percentage of output queues for the task node.|

    You can click a task node to enter its details page, and view its **Subtask List**.

    Realtime Compute also provides **Curve Charts** for all metrics of each task. Click a task node to view the **Curve Charts**.

-   List mode

    Realtime Compute also allows you to view each task in the list mode.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41066/156584754733942_en-US.png)

    You can click a task node to enter its details page, and view its **Subtask List**.

    |Name|Description|
    |----|-----------|
    |ID|The task ID in the running topology.|
    |Name|The name of the task.|
    |Status|The status of the task.|
    |InQ max|The maximum percentage of input queues for the task node.|
    |OutQ max|The maximum percentage of output queues for the task node.|
    |RecvCnt sum|The total amount of data that is received by the task node.|
    |SendCnt sum|The total amount of data that is sent from the task node.|
    |TPS sum|The total amount of data that is read from the input source every second.|
    |Delay max|The maximum processing delay at the task node.|
    |StartTime|The start time of the task node.|
    |Durations\(s\)|The running duration of the task node, in seconds.|
    |Task|The running status of the parallel subtasks under the task node.|


