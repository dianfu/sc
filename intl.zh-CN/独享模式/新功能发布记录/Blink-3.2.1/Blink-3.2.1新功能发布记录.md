# Blink-3.2.1新功能发布记录

本文为您介绍Blink-3.2.1版本的核心功能以及新版本对于Datasteam和SQL的兼容性。

## 版本核心功能介绍

Blink-3.2.1是Blink正式开源后第1个基于开源代码的正式版本。Blink-3.2.1版本核心功能介绍如下：

-   Job AutoScale

    Blink-3.2.1版本中加入了AutoConfig和AutoScale相结合的自动调优功能。自动调优功能能够在作业运行过程中，根据作业运行状态以及输入流量，自动调整各个算子的并发和资源， 从而保证作业的低延时。在Blink-3.2.1版本中，该功能处于公测阶段。

-   支持Datastream API
    -   Blink-3.2.1版本中正式加入了对Datastream API的支持。Blink-3.2.1基于开源Flink 1.5.1分支开发，Blink-3.2.1中Datastream API接口与开源Flink1.5.1版本兼容性对比请参见[Blink 3.2与Flink 1.5.1版本API兼容性报告](/intl.zh-CN/独享模式/新功能发布记录/Blink-3.2.1/Blink 3.2与Flink 1.5.1版本API兼容性报告.md)。
    -   Datastream Connector新增

        |对接系统|Source|Sink|
        |----|------|----|
        |Kafka|兼容|兼容|
        |HBase|不兼容|
        |JDBC|
        |RDS<MySQL\>|
        |ES|
        |MongoDB|

-   SQL Connector新增

    Blink-3.2.1在Blink2.0版本基础上新增了如下Connector。

    |对接系统|Connector类型|
    |----|-----------|
    |ES|DIM|
    |MongoDB|SINK|
    |Redis|DIM|
    |Redis|SINK|
    |RocketMQ（4.2.0）|SOURCE|
    |SQL Server|SINK|


## 兼容性

-   Datastream兼容性请参见[Blink 3.2与Flink 1.5.1版本API兼容性报告](/intl.zh-CN/独享模式/新功能发布记录/Blink-3.2.1/Blink 3.2与Flink 1.5.1版本API兼容性报告.md)。
-   SQL兼容性请参见[Blink 3.0与Blink 2.0版本SQL不兼容项汇总](/intl.zh-CN/独享模式/新功能发布记录/Blink-3.2.1/Blink 3.0与Blink 2.0版本SQL不兼容项汇总.md)。

