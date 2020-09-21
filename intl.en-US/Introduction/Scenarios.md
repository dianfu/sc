---
keyword: [existing stream processing system, enterprise department, technology]
---

# Scenarios

Realtime Compute for Apache Flink is widely used for real-time big data computing. This topic describes how to apply Realtime Compute for Apache Flink to different enterprise departments and technologies.

## Existing stream processing system

If you have installed the Flink system on your local server, you can directly migrate it to Realtime Compute for Apache Flink. If you have installed the Storm or Spark Streaming system on your local server, you can rebuild and then migrate it to Realtime Compute for Apache Flink.

![Migration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3666930061/p44714.png)

## Enterprise departments

Realtime Compute for Apache Flink provides different features for the following departments:

![Departments](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3666930061/p44715.png)

-   Business department: real-time risk control, real-time recommendation, and real-time indexing of search engines.
-   Data department: real-time data warehousing, real-time reports, and real-time dashboards
-   O&M department: real-time monitoring, real-time exception detection and alerting, and end-to-end debugging.

## Technologies

From the technical perspective, Realtime Compute for Apache Flink is suitable for the following scenarios:

-   Real-time extract, transform, load \(ETL\) and data streams

    Data is delivered from point A to point B by using the real-time ETL procedure and data streams. During data delivery, data cleansing and integration may be required, such as real-time indexing in the search system and ETL procedure in real-time data warehousing.

    ![ETL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3676930061/p44716.png)

-   Real-time data analysis

    Data analysis is a process to extract and integrate required information from raw data to achieve your business objectives. For example, you can view the top 10 products sold per day, the average turn-around time in the warehouse, the average document click rate, and the open rate for push notifications. Real-time data analysis allows you to view real-time reports or dashboards.

    ![Real-time data analysis](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3676930061/p44717.png)

-   Event-driven applications

    An event-driven application is a system that processes or reacts to subscription events. Event-driven applications depend on internal states and respond to suspicious events detected during fraud detection or in the risk control system or O&M exception detection system. If the behavior of a user triggers a risk control point, the system captures the event and analyzes the current and previous user behavior to determine whether to perform risk control over the user.

    ![Event-driven](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3676930061/p44718.png)


**Note:** The preceding technology flowcharts are obtained from [Apache Flink official website](https://flink.apache.org/usecases.html).

