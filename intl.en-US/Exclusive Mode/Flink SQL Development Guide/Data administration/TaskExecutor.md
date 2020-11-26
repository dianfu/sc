# TaskExecutor

This topic describes the role of TaskExecutors in the startup process of a Realtime Compute for Apache Flink cluster and explains the TaskExecutor tab.

**Note:** This topic applies to only Realtime Compute for Apache Flink whose version is earlier than V3.0.

## Background information

TaskExecutors are an indispensable part in starting a Realtime Compute for Apache Flink cluster. TaskExecutors receive tasks from and return execution results to the JobManager. The number of slots is specified when a TaskExecutor is started. Only one task thread can be executed in each slot. A TaskExecutor receives tasks from the JobManager, and then builds a Netty connection with its upstream to receive and process data.

## Go to the TaskExecutor tab

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  In the **Jobs** section, click the name of the required job under the **Job Name** field.
4.  On the **Job Administration** page, click the **TaskExecutor** tab.

## Role of TaskExecutors in cluster startup

TaskExecutors are an indispensable part in starting a Realtime Compute for Apache Flink cluster. The following items describe the startup process of a Realtime Compute for Apache Flink cluster:

1.  When a Realtime Compute for Apache Flink cluster is started, one JobManager and several TaskExecutors are started at the same time.
2.  The client submits tasks to the JobManager.
3.  The JobManager assigns tasks to TaskExecutors.
4.  The TaskExecutors report the heartbeat and statistical information to the JobManager.

## TaskExecutor tab

The TaskExecutor tab provides a list of tasks and the entries to their details.

![taskexecutor](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5321659951/p34013.png)

