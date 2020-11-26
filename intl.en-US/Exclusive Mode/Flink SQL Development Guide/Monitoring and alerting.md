# Monitoring and alerting

This topic describes the monitoring and alerting process in Realtime Compute for Apache Flink and how to create alert rules in Realtime Compute for Apache Flink.

## Introduction to Cloud Monitor

Cloud Monitor helps you collect the monitoring metrics of cloud resources or other custom monitoring metrics, check service availability, and set alerts based on these monitoring metrics. Cloud Monitor helps you view the cloud resource usage, business running information, and service health status. In addition, you can receive and respond to alerts in a timely manner to keep applications running properly.

## View monitoring metrics

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  On the **Administration** page, click the name of the job whose monitoring metrics you want to view.
4.  In the upper-right corner of the Job Administration page, move the pointer over the **More** icon and click **Monitor**.
5.  On the page that appears, view the monitoring metrics of the job.

## Create an alert rule

For more information about how to create an alert rule, see [Configure alert rules](/intl.en-US/.md).

**Note:** The failover rate indicates the average number of failovers per second in the last minute. For example, if one failover occurred in the last minute, the failover rate is 0.01667 \(1/60 = 0.01667\).

