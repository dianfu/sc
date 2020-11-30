# Workflow

This topic describes the architecture and data links in the workflow of Alibaba Cloud Realtime Compute for Apache Flink.

## Architecture

The following figure shows the architecture of Realtime Compute for Apache Flink.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2096930061/p21873.png)

1.  Data collection

    You can use streaming data collection tools to collect and send streaming data in real time to a publish-subscribe system for big data analysis. This publish-subscribe system continuously produces events for Realtime Compute for Apache Flink in the downstream to trigger stream processing jobs. The Alibaba Cloud big data ecosystem offers publish-subscribe systems for big data analysis in different scenarios. Realtime Compute for Apache Flink integrates multiple publish-subscribe systems shown in the preceding figure and therefore can integrate various data streams.

    **Note:** For example, you can directly connect Realtime Compute for Apache Flink to LogHub of Log Service to quickly integrate and use ECS logs.

2.  Stream processing

    Data streams continuously enter Realtime Compute for Apache Flink for real-time processing. At least one data stream must enter Realtime Compute for Apache Flink to trigger a Realtime Compute for Apache Flink job. In complex business scenarios, Realtime Compute for Apache Flink allows you to perform JOIN operations on the static data of data stores.

    **Note:** For example, you can perform JOIN operations on DataHub and ApsaraDB RDS data based on the primary key of streaming data.

3.  Real-time integration

    Realtime Compute for Apache Flink can directly write the result data of stream processing into the destination data store. Realtime Compute for Apache Flink integrates Alibaba Cloud ecosystems such as OLTP \(for example, ApsaraDB RDS\), NoSQL \(for example, Tablestore\), OLAP \(for example, AnalyticDB for MySQL\), Message Queue \(for example, DataHub and ONS\), and MassiveStorage \(for example, OSS and MaxCompute\). This minimizes the end-to-end data latency and complexity of data links and ensures real-time data processing.

4.  Data consumption

    After the result data of stream processing is written into a storage system, you can use customized applications to manage the result data. You can use a storage system to access the result data, data transmission system to receive the result data, or alerting system to send alerts.


## Data links

Some Alibaba Cloud ecosystems do not support Realtime Compute for Apache Flink. You must convert the streaming data of these storage systems to other data types.

-   LogService

    Log Service is an end-to-end service for log data. It allows you to quickly collect, transfer, query, consume, and analyze log data. For more information about how to use logs to collect streaming data, see [Log collection methods](/intl.en-US/Data Collection/Log collection methods.md).

-   IoTHub

    IoT Hub helps developers build secure data channels to implement bidirectional communications between the cloud and terminal devices. The terminal devices include sensors, actuators, embedded devices, and smart home appliances. You can use the IoT Hub rules engine to easily transfer IoT data to DataHub and use Realtime Compute for Apache Flink and MaxCompute to perform computations on the data. For more information about how to transfer IoT data to DataHub, see [Configure data forwarding rules](/intl.en-US/Communications/Data Forwarding/Configure data forwarding rules.md).

-   MQ

    Alibaba Cloud Message Queue is a complete messaging service. It provides features such as publishing and subscription, message tracing, resource statistics, timing \(latency\), and monitoring and alerting based on distributed clusters in high availability mode.


