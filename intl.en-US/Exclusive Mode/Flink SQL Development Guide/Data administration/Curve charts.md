# Curve charts

Realtime Compute for Apache Flink shows core metrics of your job on the Curve Charts tab to help you diagnose the running status of the job. In the future, Realtime Compute for Apache Flink will provide more deep intelligent analysis algorithms to help you perform intelligent and automated diagnosis based on the status of jobs.

The following figure shows the curve chart of a metric.

![Curve Charts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3321659951/p33954.png)

**Note:**

-   The metrics are displayed only when a Realtime Compute for Apache Flink job is in the **running** state. If the job is **suspended** or **terminated**, the metrics of this job do not appear.
-   The metrics are collected and displayed on the Curve Charts tab after the job is running for more than one minute. This causes the latency of the data presented in the curve charts.

## Go to the Curve Charts page

1.  Go to the **Job Administration** page.
    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
    2.  In the top navigation bar, click **Administration**.
    3.  On the **Jobs** page that appears, click the target job name under the **Job Name** field.
2.  On the top of the **Job Administration** page, click the **Curve Charts** tab.

## OverView

-   **Failover**

    The failover curve chart displays the frequency of failover caused by errors or exceptions for the current job. To calculate the failover rate, divide the total number of failovers that occurred within the minute that preceded the current failover time by 60. For example, if a failover occurred once in the last minute. The failover rate is 0.01667. The number is calculated based on the following formula: 1/60 = 0.01667.

-   **Delay**

    To help you obtain the full-link timeliness and job performance, Realtime Compute for Apache Flink provides the following three latency metrics:

    -   **Processing Delay**: Processing delay = Current system time - Event time at which the system processes the last data record. If no more data enters input storage systems, the processing delay gradually increases as the system time continues to move forward.
    -   **Data Pending Time**: Data pending time = Time when data enters Realtime Compute for Apache Flink - Event time. Even if no more data enters input storage systems, the queued time does not increase. The queued time is used to assess whether the Realtime Compute for Apache Flink job has back pressure.
    -   **Data Arrival Interval**: Data arrival interval = Processing delay - Data pending time. If the Realtime Compute for Apache Flink job has no back pressure, the queued time is short and stable. In this case, this metric reflects the degree of data sparsity between the data sources. If the Realtime Compute for Apache Flink job has back pressure, the queued time is long or unstable. In this case, this metric cannot be used for reference.
    **Note:**

    -   Realtime Compute for Apache Flink uses a distributed computing framework. The preceding three latency metrics obtain values of each shard or partition of the data source. Then, the metrics report the maximum values among all the shards or all the partitions to the development platform of Realtime Compute for Apache Flink. Therefore, the aggregated data arrival interval displayed on the development platform is different from that obtained based on the formula: Data arrival interval = Processing delay - Data pending time.
    -   If no more data enters a shard or a partition of the data source, the processing delay increases gradually.
-   **Input TPS of Each Source**

    This chart reflects statistics on all streaming data input of a Realtime Compute for Apache Flink job. The chart records the number of blocks that are read from the source table per second. This helps you obtain the transactions per second \(TPS\) of a data storage system. Different from TPS, the records per second \(RPS\) metric indicates the number of records read from the source table per second. These records are resolved from the blocks. Assume that Log Service reads five log groups per second. The TPS is 5. If eight log records are resolved from each log group, a total of 40 log records are resolved. The RPS is 40.

-   **Data Output of Each Sink**

    This chart reflects statistics on all output data of a Realtime Compute for Apache Flink job. This helps you obtain the RPS of a data storage system. In most cases, if no data output is detected during system operations and maintenance \(O&M\), you must check the input of the upstream storage system and the output of the downstream storage system.

-   **Input RPS of Each Source**

    This chart reflects statistics on all input streaming data of a Realtime Compute for Apache Flink job. This helps you obtain the RPS of a data storage system. If no data output is detected during system O&M, you must check the RPS to determine whether the input data from the upstream storage system is normal.

-   **Input BPS of Each Source**

    This chart reflects statistics on all input streaming data of a Realtime Compute for Apache Flink job. This chart records the traffic that is used to read the input source table per second. This helps you obtain the bytes per second \(BPS\) of the traffic.

-   **Dirty Data from Each Source**

    This chart reflects the number of dirty data records in the data source of a Realtime Compute for Apache Flink job in different time periods.

-   **Auto Scaling Successes and Failures**

    This chart reflects the number of auto scaling successes and the number of auto scaling failures.

    **Note:** This curve chart is applicable to only Realtime Compute for Apache Flink whose version is later than V3.0.0.

-   **CPUs Consumed By Auto Scaling**

    This chart reflects the number of CPUs consumed when auto scaling is executed.

    **Note:** This curve chart is applicable to only Realtime Compute for Apache Flink whose version is later than V3.0.0.

-   **Memory Consumed by Auto Scaling**

    This chart reflects the memory space consumed when auto scaling is executed.

    **Note:** This curve chart is applicable to only Realtime Compute for Apache Flink whose version is later than V3.0.0.


## Advanced View

Alibaba Cloud Realtime Compute for Apache Flink provides a fault tolerance mechanism that allows you to restore data streams and ensures that the data streams are consistent with the application. The function of the fault tolerance mechanism is to create consistent snapshots of distributed data streams and the related states. These snapshots work as consistency checkpoints to which the system can fall back if a failure occurs.

One of the core concepts for distributed snapshots is barriers. Barriers are inserted into data streams. Then, the data streams that contain the barriers flow to the downstream. Barriers do not overtake data records. The records flow strictly in line. A barrier divides a data stream into two parts. One part enters the current snapshot and the other part enters the next snapshot. Each barrier has a snapshot ID. If the data flows before a barrier is inserted in the data stream, the data is included in the specified snapshot. Barriers are light-weight. Barriers do not interfere with the processing of data streams. Multiple barriers from different snapshots can co-exist in the same data stream. This allows multiple snapshots to be concurrently created.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3321659951/p33962.png)

Barriers are inserted into data streams at the source end. When a barrier from Snapshot n is inserted, the system automatically records the checkpoint of Snapshot n in the data stream. This checkpoint is indicated by Sn. Then, the barrier continues flowing to the downstream. When an intermediate operator receives barriers for Snapshot n from all the input streams, the operator emits only one barrier for Snapshot n to all the output streams. When the sink operator that is the destination of the directed acyclic graph \(DAG\) stream receives Barrier n from each of its input streams, the operator acknowledges to the checkpoint coordinator that Snapshot n is created. After all the sink operators acknowledge that Snapshot n is created, this snapshot is considered completed.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3321659951/p33963.png)

The following table describes the curve charts of checkpoint metrics.

|Curve chart|Description|
|-----------|-----------|
|Checkpoint Duration|Displays the time that is consumed to create a checkpoint. Unit: milliseconds.|
|Checkpoint Size|Displays the memory size that is required to create a checkpoint.|
|Checkpoint Alignment Time|Displays the duration consumed by all the data streams to flow from the upstream nodes to the node on which you create a checkpoint. When the sink operator receives Barrier n from all the input streams, the operator acknowledges to the checkpoint coordinator that Snapshot n is created. The sink operator represents the destination of the DAG stream. After all the sink operators acknowledge that snapshot n is created, this snapshot is considered completed. This duration is known as the checkpoint alignment time.|
|Checkpoint Count|Displays the number of checkpoints within a specific period of time.|
|Get|Displays the longest time that a subtask spends in performing a GET operation on the RocksDB within a specific period of time.|
|Put|Displays the longest time that a subtask spends in performing a PUT operation on the RocksDB within a specific period of time.|
|Seek|Displays the longest time that a subtask spends in performing a SEEK operation on the RocksDB within a specific period of time.|
|State Size|Displays the state size of the job within a specific period of time. If the size increases at a high rate, we recommend that you check for potential issues in the job.|
|GMS GC Time|Displays the time that the underlying container of the job spends on garbage collection.|
|GMS GC Rate|Displays the frequency at which the underlying container of the job performs garbage collection.|

## WaterMark

|Curve chart|Description|
|-----------|-----------|
|Watermark Delay|Displays the difference between the watermark time and the system time.|
|Dropped Records per Second|Displays the number of data records that are dropped per second. If a data record arrives at the window after the watermark time, the data record is dropped.|
|Dropped Records|Displays the total number of dropped data records. If a data record arrives at the window after the watermark time, the data record is dropped.|

## Delay

**Top 15 Source Subtasks with the Longest Processing Delay**

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3321659951/p33978.png)

This chart displays the processing delay of each source subtask.

## Throughput

|Curve chart|Description|
|-----------|-----------|
|Task Input TPS|Displays the data input status of all the tasks in a job.|
|Task Output TPS|Displays the data output status of all the tasks in a job.|

## Queue

|Curve chart|Description|
|-----------|-----------|
|Input Queue Usage|Displays the data input queue of all the tasks in a job.|
|Output Queue Usage|Displays the data output queue of all the tasks in a job.|

## Tracing

|Curve chart|Description|
|-----------|-----------|
|Time Used In Processing Per Second|Displays the time that a task spends in processing data per second.|
|Time Used In Waiting Output Per Second|Displays the time that a task spends in waiting for output data per second.|
|Task Latency Histogram Mean|Displays the latency of each task.|
|Wait Output Histogram Mean|Displays the time that each task spends in waiting for output.|
|Wait Input Histogram Mean|Displays the time that each task spends in waiting for input.|
|Partition Latency Mean|Displays the latency of concurrent tasks in each partition.|

## Process

|Curve chart|Description|
|-----------|-----------|
|Process Memory RSS|Displays the memory usage of each process.|
|CPU Usage|Displays the CPU utilization of each process.|

## JVM

|Curve chart|Description|
|-----------|-----------|
|Memory Heap Used|Displays the Java Virtual Machine \(JVM\) heap memory usage of a job.|
|Memory Non-Heap Used|Displays the JVM non-heap memory usage of a job.|
|Thread Count|Displays the number of threads in a job.|
|GC\(CMS\)|Displays the number of times that a job completes garbage collection.|

