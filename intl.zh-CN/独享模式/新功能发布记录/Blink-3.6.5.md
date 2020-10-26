# Blink-3.6.5

本文为您介绍Blink 3.6.5版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

-   优化DataHub源表CPU使用率过高的问题。
-   优化维表的partitionJOIN功能，支持缓存策略为All的维表加载分区。此外，引入多维表异步并行优化，所有支持异步并行优化的维表可以在WITH参数中，设置partitionedJoin = 'true'，开启partitionJOIN功能。
-   日志服务SLS源表新增startupMode参数。参数取值如下：

    -   TIMESTAMP：每个Shard从指定时间开始消费。
    -   Earlist：每个Shard从最早位置开始消费。
    -   Latest：每个Shard从最新位置开始消费。
    -   Group\_Offsets：每个Shard优先从服务端保存的Checkpoint开始消费，必须指定ConsumerGroup。
    **说明：** 仅当State中不存在Checkpoint时，Earlist、Latest、Group\_Offsets和Timestamp的配置才生效。

-   降低大规模作业拓扑的内存消耗。
-   Oracle源表的timeFieldType参数支持多种时间格式：
    -   TO\_DATE：DATE类型。
    -   TIMESTAMP：TIMESTAMP类型。
    -   VARCHAR：日期字符串类型。
    -   NUMBER：数字类型。
    -   实现OracleSourceQueryCondition接口并配置类名。

## 主要缺陷修复

-   修复在一个Task Manager存在多个线程的情况下，开启partitionJOIN功能后，MaxCompute维表存在错误数据的缺陷。
-   修复Sink DDL field type与实际插入数据类型不一致时出现报错的缺陷。
-   修复SLS源表不消费数据的缺陷。

