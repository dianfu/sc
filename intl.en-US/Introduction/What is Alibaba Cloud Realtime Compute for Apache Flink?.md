---
keyword: [Realtime Compute for Apache Flink, what is Alibaba Cloud Realtime Compute for Apache Flink]
---

# What is Alibaba Cloud Realtime Compute for Apache Flink?

Alibaba Cloud Realtime Compute for Apache Flink is powered by Ververica and developed by Alibaba Cloud based on Apache Flink. It is an enterprise-level, high-performance system that is used to process big data in real time. It is officially released by the founding team of Apache Flink and has a globally uniform commercial brand. This system is fully compatible with the APIs of open source Flink and provides a wide range of value-added features for enterprises.

## Benefits

-   High performance: The job throughput can achieve millions of data records per second. The computing latency is reduced to seconds. The TPC-H performance test result shows that the performance of Realtime Compute for Apache Flink is three to five times that of the open source Flink.
-   Powerful functions: An end-to-end web console is provided for project development and allows you to monitor dozens of job metrics. An intelligent diagnosis system is provided. Intelligent optimization for job performance is supported.
-   Cost-effectiveness: Ultimate elasticity is provided. The pay-as-you-go billing method is supported. The total cost of cloud resources is lower than that of open source Flink.
-   Guaranteed stability and security: The service level agreement \(SLA\) guarantees 99.9% availability. A distributed computing engine is used to support fault tolerance. Faults can be automatically recovered. Tenants are isolated to prevent mutual interference.
-   Outstanding branding: Realtime Compute for Apache Flink is officially released by the founding team of Apache Flink and certified by China Academy of Information and Communications Technology \(CAICT\). It is recognized as a real-time stream processing product in the Forrester Wave.
-   Compatibility with APIs of open source Flink: The latest Flink version is used. It is fully compatible with APIs of open source Flink. This allows you to smoothly migrate your business to the cloud.

## Architecture

![Architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8203643061/p141022.png)

## Comparison with Apache Flink

![Comparison](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9203643061/p141885.png)

## Development history

The development of Alibaba Cloud Realtime Compute for Apache Flink includes two phases:

1.  **Blink+Bayes**

    Phase 1 \(2016 to 2019\): In this phase, Alibaba Cloud Realtime Compute for Apache Flink was developed based on the Blink computing engine and the Bayes development platform. Alibaba Cloud Realtime Compute for Apache Flink provided two types of clusters: Blink exclusive cluster and Blink shared cluster.

    -   Blink

        Blink is an enterprise-level computing engine developed by Alibaba Cloud based on Flink 1.5. Compared with open source Flink, Blink has the following features:

        -   Optimized SQL statements: Features such as miniBatch, local aggregation, efficient deduplication, and TopN are added to SQL statements.
        -   Optimized runtime: Incremental checkpoints and asynchronous I/O are implemented. The cluster scheduling policy and failover policy are optimized.
        -   Seamless integration with various cloud data stores: Blink can integrate with more than 10 types of cloud data stores, such as Log Service, DataHub, Message Queue for Apache Kafka, Tablestore, ApsaraDB RDS, MaxCompute, ApsaraDB for HBase, and Elasticsearch.
        -   High performance: In some scenarios, Blink provides performance five to ten times higher than open source Flink.
        -   Stable and reliable for large-scale deployment: Blink provides high stability and reliability in large-scale deployment scenarios such as Double 11.
    -   Bayes

        ![Development platform](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9203643061/p141887.png)

        Bayes is the development platform of Realtime Compute for Apache Flink. This platform is developed by Alibaba Cloud. It provides the following features:

        -   Data storage
        -   Local debugging
        -   Monitoring and alerts
        -   Graphical resource configuration
        -   Rich metric curve charts
        -   Automatic resource optimization by using automatic configuration and automatic scaling
        -   Publishing and running of DataStream and Flink SQL jobs
2.  **VVR+VVP**

    Phase 2 \(2019 till now\): In this phase, Alibaba Cloud Realtime Compute for Apache Flink has been developed based on the Ververica Runtime \(VVR\) computing engine and the Ververica Platform \(VVP\). Alibaba Cloud Realtime Compute for Apache Flink provides two types of services: semi-managed and fully-managed Flink.

    ![vvp+vvr](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9203643061/p141026.png)

    -   VVR

        VVR is an enterprise-level computing engine developed by Alibaba Cloud and is fully compatible with open source Flink. Compared with open source Flink, VVR has the following features:

        -   It provides business GeminiStateBackend, which brings the following benefits:
            -   Uses a new data structure to increase the random query speed and reduce frequent disk I/O operations.
            -   Optimizes the cache policy. If memory is sufficient, hot data is not stored in disks and cache entries do not expire after compaction.
            -   Uses Java to implement GeminiStateBackend, which eliminates Java Native Interface \(JNI\) overheads caused by RocksDB.
            -   Uses off-heap memory and implements an efficient memory allocator based on GeminiDB to eliminate the impact of garbage collection for Java Virtual Machine \(JVM\).
            -   Supports asynchronous incremental checkpoints. This ensures that only memory indexes are copied during data synchronization. Compared with RocksDB, GeminiStateBackend avoids jitters caused by I/O operations.
            -   Supports local recovery and storage of the timer.
        -   It supports various connectors, such as Log Service, DataHub, Message Queue for Apache Kafka, and Tablestore. The supported connectors are continuously updated.
        -   It is fully compatible with open source Flink and follows the release updates of open source Flink.
    -   VVP

        ![VVP](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4208763061/p141886.png)

        VVP is the development platform of Realtime Compute for Apache Flink. This platform is developed by Alibaba Cloud and the Apache Flink founding team. It provides the following features:

        -   Monitoring and alerts
        -   Support for savepoints
        -   Rich metric curve charts
        -   Automatic resource optimization by using AutoPilot
        -   Publishing and running of DataStream and Flink SQL jobs

## References

-   For more information about the service types and selection of Realtime Compute for Apache Flink, see [Service types](/intl.en-US/Introduction/Service types.md).
-   For more information about the application scenarios of Realtime Compute for Apache Flink, see [Scenarios](/intl.en-US/Introduction/Scenarios.md).

