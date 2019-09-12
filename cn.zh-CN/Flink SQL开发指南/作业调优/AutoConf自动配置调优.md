# AutoConf自动配置调优 {#concept_66930_zh .concept}

为了增加用户的体验度，实时计算团队开发了自动配置调优（AutoConf）功能。

**说明：** AutoConf自动配置调优功能支持blink 1.0和2.0版本。

## 背景及功能范围 {#section_g5m_wts_zfb .section}

在您作业的各个算子和流作业上下游性能达标和稳定的前提下，自动配置调优功能可以帮助您更合理的分配各算子的资源和并发度等配置。 全局优化您的作业，调节作业吞吐量不足、作业全链路的反压等性能调优的问题。

出现下列情况时，自动配置调优功能可以作出优化，但无法彻底解决流作业的性能瓶颈，需要您自行解决或联系实时计算产品支持团队解决性能瓶颈。

-   流作业上下游有性能问题。
    -   流作业上游的数据Source存在性能问题。例如，DataHub分区不足、MQ吞吐不够等需要您扩大相应Source的分区。
    -   流作业下游的数据Sink存在性能问题。例如，RDS死锁等。
-   流作业的[自定义函数](cn.zh-CN/Flink SQL开发指南/Flink SQL/自定义函数（UDX）/UDX概述.md#) （UDF、UDAF和UDTF） 有性能问题。

## 操作 {#section_mzn_c5s_zfb .section}

-   新作业
    1.  上线作业
        1.  完成SQL开发，通过**语法检查**后，单击**上线**，即可出现如下**上线新版本**界面。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333876_zh-CN.png)

        2.  单击**智能CU配置**。第一次不需要指定CU数直接使用系统默认配置。
            -   **智能配置**：指定使用CU AutoConf算法会基于系统默认配置，生成CU数，进行优化资源配置。如果是第一次运行，算法会根据经验值生成一份初始配置。建议作业运行了5~10分钟以上，确认Source RPS等Metrics稳定2~3分钟后，再使用智能配置，重复3~5次才能调优出最佳的配置。
            -   **使用上次资源配置**：即使用最近一次保存的资源配置。如果上一次是智能配置的，就使用上一次智能配置的结果。如果上一次是手工配置的，就使用上次手工配置的结果。
    2.  使用默认配置启动作业

        1.  使用默认配置启动作业，出现如下的界面。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333877_zh-CN.png)

        2.  **启动**作业。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333878_zh-CN.png)

        示例如下。第一次默认配置生成的资源配置为71个CU。

        **说明：** 请您确保作业已经运行10分钟以上，并且Source RPS等数据曲线稳定2-3分钟后，再使用智能CU配置。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333879_zh-CN.png)

    3.  使用智能配置启动作业
        1.  资源调优

            例如，您手动配置40CU，使用智能模式启动。40的CU数是您自行指定调整的，您可以根据具体的作业情况适当的增加或者是减小CU数，来达到资源调优的目的。

            -   CU的最小配置

                CU的最小配置建议不小于默认配置总数的50%，CU数不能小于1CU。假设智能配置默认CU数为71，则建议最小CU数为36CU。 71\*50% = 35.5CU。

            -   CU增加数量

                假如无法满足作业理想的吞吐量就需要增加适量的CU数。每次增加的CU数，建议是上一次CU总数的30%以上。例如，上一次配置是10CU，下次就需要增加到13CU。

            -   可多次调优

                如果第一次调优不满足您的需求，可以调优多次。可以根据每次调优后Job的状态来增加或减少资源数。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333880_zh-CN.png)

        2.  调优后的结果如下图。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333881_zh-CN.png)

            **说明：** 如果是新任务，请不要选择**使用上次资源配置**，否则会报错。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333882_zh-CN.png)

-   已存在作业
    -   调优流程示意图

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689333883_zh-CN.png)

        **说明：** 

        -   已存在的作业在进行调优前，您一定要检查是否是有状态的计算。因为在调优的过程中可能会清除之前作业保存的状态，请您谨慎操作。
        -   当您的作业有改动时（例如，您对作业的SQL有修改，或更改了实时计算版本），自动配置调优功能不保证能按照之前的运行信息进行调优。原因是这些改动会导致拓扑信息变化，造成数据曲线无法对应和状态无法复用等问题。自动配置调优功能无法根据运行历史信息作出调优判断。此时再使用自动配置调优会报异常。这时您需要将改动后的任务当做新任务，重新进行操作。
    -   调优流程
        1.  **暂停**任务。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689433884_zh-CN.png)

        2.  重复新作业的调优步骤，使用最新的配置启动作业。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689433885_zh-CN.png)


## 常见问题 {#section_plc_mn1_fhb .section}

以下几点可能会影响自动调优的准确性：

-   任务运行的时间较短，会造成采样得到的有用信息较少，会影响AutoConf算法的效果。建议延长运行时间，确认Source RPS等数据曲线稳定2~3分钟后即可。
-   任务运行有异常（Failover），会影响结果的准确性。建议用户检查和修复Failover的问题。
-   任务的数据量比较少，会影响结果的准确性。建议回追足够多的历史数据。
-   影响的因素有很多，自动调优AutoConf不能保证下一次生成的配置一定比上一次的好。如果还不能满足需求，用户参考[手动配置调优](cn.zh-CN/Flink SQL开发指南/作业调优/手动配置调优.md#)，进行手动调优。

## 调优建议 {#section_yh4_mn1_fhb .section}

-   每次触发智能配置前任务稳定运行超过10分钟。这样有利于AutoConf准确搜集的任务运行时的指标信息。
-   AutoConf可能需要3~5次迭代才能见效。
-   使用AutoConf时，您可以设置让任务回追数据甚至造成反压。这样会更有利于快速体现调优成功。

## 如何判断自动配置调优功能生效或出现问题？ {#section_j4c_nn1_fhb .section}

自动配置调优功能通过JSON配置文件与实时计算交互。您在调优后，可以通过查看JSON配置文件了解自动配置调优功能的运行情况。

-   查看JSON配置文件的两种方式
    1.  通过作业编辑界面，如下图。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689433886_zh-CN.png)

    2.  通过作业运维界面，如下图。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41063/156825689433887_zh-CN.png)

-   JSON配置解释

    ``` {#codeblock_qfl_dbp_4mw .language-xml}
    "autoconfig" : {
        "goal": {  // AutoConf 目标
            "maxResourceUnits": 10000.0,  // 单个Blink作业最大可用CU数，不能修改，查看时可忽略。
            "targetResoureUnits": 20.0  // 用户指定CU数。用户指定为20CU。
        }，
        "result" : {  // AutoConf 结果。这里很重要
          "scalingAction" : "ScaleToTargetResource",  // AutoConf的运行行动 *
          "allocatedResourceUnits" : 18.5, // AutoConf分配的总资源。
          "allocatedCpuCores" : 18.5,      // AutoConf分配的总CPU。
          "allocatedMemoryInMB" : 40960    // AutoConf分配的总内存。
          "messages" : "xxxx"  // 很重要。 *
        }
    }
    ```

    -   scalingAction：`InitialScale`代表初次运行， `ScaleToTargetResource`代表非初次运行。
    -   如果没有messages，代表运行正常。如果有messages，代表需要分析：messages有两种，如下所示：
        -   warning 提示：表示正常运行情况但有潜在问题，需要用户注意，如source的分区不足等。
        -   error或者exception提示，常伴有`Previous job statistics and configuration will be used`，代表AutoConf失败。失败也有两种原因：
            -   用户作业或blink版本有修改，AutoConf无法复用以前的信息。
            -   有exception代表AutoConf遇到问题，需要跟据信息、日志等综合分析。如没有足够的信息，请提交工单。

## 异常信息问题 {#section_awr_nn1_fhb .section}

 **IllegalStateException异常** 

出现如下的异常说明内部状态state无法复用，需要**停止**任务清除状态后重追数据。

如果无法切到备链路，担心对线上业务有影响，可以在**开发**界面右侧的**作业属性**里面选择上个版本进行回滚，等到业务低峰期的时候再重追数据。

``` {#codeblock_3l9_bqk_xi1}
java.lang.IllegalStateException: Could not initialize keyed state backend.
    at org.apache.flink.streaming.api.operators.AbstractStreamOperator.initKeyedState(AbstractStreamOperator.java:687)
    at org.apache.flink.streaming.api.operators.AbstractStreamOperator.initializeState(AbstractStreamOperator.java:275)
    at org.apache.flink.streaming.runtime.tasks.StreamTask.initializeOperators(StreamTask.java:870)
    at org.apache.flink.streaming.runtime.tasks.StreamTask.initializeState(StreamTask.java:856)
    at org.apache.flink.streaming.runtime.tasks.StreamTask.invoke(StreamTask.java:292)
    at org.apache.flink.runtime.taskmanager.Task.run(Task.java:762)
    at java.lang.Thread.run(Thread.java:834)
Caused by: org.apache.flink.api.common.typeutils.SerializationException: Cannot serialize/deserialize the object.
    at com.alibaba.blink.contrib.streaming.state.AbstractRocksDBRawSecondaryState.deserializeStateEntry(AbstractRocksDBRawSecondaryState.java:167)
    at com.alibaba.blink.contrib.streaming.state.RocksDBIncrementalRestoreOperation.restoreRawStateData(RocksDBIncrementalRestoreOperation.java:425)
    at com.alibaba.blink.contrib.streaming.state.RocksDBIncrementalRestoreOperation.restore(RocksDBIncrementalRestoreOperation.java:119)
    at com.alibaba.blink.contrib.streaming.state.RocksDBKeyedStateBackend.restore(RocksDBKeyedStateBackend.java:216)
    at org.apache.flink.streaming.api.operators.AbstractStreamOperator.createKeyedStateBackend(AbstractStreamOperator.java:986)
    at org.apache.flink.streaming.api.operators.AbstractStreamOperator.initKeyedState(AbstractStreamOperator.java:675)
    ... 6 more
Caused by: java.io.EOFException
    at java.io.DataInputStream.readUnsignedByte(DataInputStream.java:290)
    at org.apache.flink.types.StringValue.readString(StringValue.java:770)
    at org.apache.flink.api.common.typeutils.base.StringSerializer.deserialize(StringSerializer.java:69)
    at org.apache.flink.api.common.typeutils.base.StringSerializer.deserialize(StringSerializer.java:28)
    at org.apache.flink.api.java.typeutils.runtime.RowSerializer.deserialize(RowSerializer.java:169)
    at org.apache.flink.api.java.typeutils.runtime.RowSerializer.deserialize(RowSerializer.java:38)
    at com.alibaba.blink.contrib.streaming.state.AbstractRocksDBRawSecondaryState.deserializeStateEntry(AbstractRocksDBRawSecondaryState.java:162)
    ... 11 more
			
```

