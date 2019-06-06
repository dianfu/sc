# Blink-3.2.1新功能发布记录 {#concept_491015 .concept}

## 版本核心功能介绍 {#section_8xw_1yd_qqm .section}

Blink-3.2.1是Blink正式开源后第1个基于开源代码的正式版本。Blink-3.2.1版本核心功能介绍如下：

-   Job AutoScale

    Blink-3.2.1版本中加入了AutoConfig和AutoScale相结合的自动调优功能。自动调优功能能够在作业运行过程中，根据作业运行状态以及输入流量，自动调整各个算子的并发和资源， 从而保证作业的低延时。在Blink-3.2.1版本中，该功能处于公测阶段，欢迎您使用。试用过程中如果遇到问题，请提交工单。

-   Datastream API支持

    -   Blink-3.2.1中正式加入了对Datastream API的支持。Blink-3.2.1基于开源Flink1.5.1分支开发，Blink-3.2.1中Datastream API接口与开源Flink1.5.1版本兼容性对比请参见[Flink 3.2与Flink 1.5.1版本API兼容性报告](cn.zh-CN/产品简介/新功能发布记录/Blink-3.2.1/Flink 3.2与Flink 1.5.1版本API兼容性报告.md#)。
    -   Datastream Connector新增

        |对接系统|Source|Sink|
        |----|------|----|
        |Kafka|兼容|兼容|
        |HBase|不兼容|
        |JDBC|
        |RDS<MySQL\>|
        |ES|
        |MangoDB|

-   SQL Connector新增

    Blink-3.2.1在Blink2.0版本基础上新增了如下Connector。

    |对接系统|Connector类型|
    |----|-----------|
    |ES|DIM|
    |MongoDB|DIM|
    |MongoDB|SINK|
    |Redis|DIM|
    |Redis|SINK|
    |RocketMQ\(4.2.0）|SOURCE|
    |DTS|SOURCE|
    |cassedra|SINK|
    |sqlserver|SINK|


## 兼容性 {#section_9df_32s_acn .section}

-   Datastream兼容性

    请参见[Flink 3.2与Flink 1.5.1版本API兼容性报告](cn.zh-CN/产品简介/新功能发布记录/Blink-3.2.1/Flink 3.2与Flink 1.5.1版本API兼容性报告.md#)。

-   SQL兼容性

    请参见[Blink 3.0与Blink 2.0版本SQL不兼容项目汇总](cn.zh-CN/产品简介/新功能发布记录/Blink-3.2.1/Blink 3.0与Blink 2.0版本SQL不兼容项目汇总.md#)。


