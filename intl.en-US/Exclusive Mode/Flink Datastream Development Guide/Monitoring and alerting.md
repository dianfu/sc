# Monitoring and alerting

This topic describes the monitoring and alerting process of Realtime Compute for Apache Flink and how to create an alert rule.

## Introduction to Cloud Monitor

Cloud Monitor helps you collect the monitoring metrics of Alibaba Cloud resources or custom monitoring metrics, check service availability, and configure alert rules based on these monitoring metrics. This service helps you view Alibaba Cloud resource usage, business running information, and service health status. In addition, you can receive and respond to alerts in a timely manner to ensure that applications are running properly.

## View monitoring metrics

1.  Log on to the [Realtime Compute for Apache Flink console](https://stream.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  On the **Administration** page, click the name of the target job.
4.  In the upper-right corner of the Job Administration page, move the pointer over the More icon and click **Monitor**.
5.  On the page that appears, view the monitoring metrics of the job.

## Create an alert rule

For more information about how to create an alert rule, see [Configure alert rules](/intl.en-US/.md).

**Note:**

-   The failover rate indicates the average number of failovers per second in the last minute. For example, if one failover occurred in the last minute, the failover rate is 0.01667 \(1/60 = 0.01667\).
-   If you use a connector provided by the open source Flink during the development of a Flink DataStream job, the monitoring metrics **service latency**, **read RPS**, and **write RPS** are not displayed in the Cloud Monitor console.

