# Flink SQL开发指南概述 {#concept_kzv_b4d_2hb .concept}

阿里实时计算开发平台为实时计算Flink SQL作业提供了存储管理、作业开发、作业调试、运维管理、监控报警和配置调优功能。

Flink SQL开发指南主要包含以下内容：

-   数据存储

    实时计算开发平台提供了实时计算上下游存储设备（例如，RDS、DataHub、OTS等）管理功能。通过存储注册方式引入的上下存储设备，可以实现数据预览、数据抽样以及DDL生成功能。数据存储详情请参见[数据存储概述](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#)。

    **说明：** 

    -   若共享模式集群需访问阿里云VPC网络下的存储资源，请参见[VPC访问授权](cn.zh-CN/Flink SQL开发指南/数据存储/VPC访问授权.md#)。
    -   若实时计算访问的上下游存储存在白名单机制，请参见[数据存储白名单配置](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储白名单配置.md#)。
-   作业开发

    作业开发章节为您介绍Flink SQL作业的[开发](cn.zh-CN/Flink SQL开发指南/作业开发/开发.md#)、[上线](cn.zh-CN/Flink SQL开发指南/作业开发/上线.md#) 和[启动](cn.zh-CN/Flink SQL开发指南/作业开发/启动.md#)流程。

-   作业调试

    作业调试章节为您介绍Flink SQL作业的调试功能，包括[本地调试](cn.zh-CN/Flink SQL开发指南/作业调试/本地调试.md#)和[线上调试](cn.zh-CN/Flink SQL开发指南/作业调试/线上调试.md#)。

-   作业运维

    作业运维章节为您介绍[运行信息](cn.zh-CN/Flink SQL开发指南/作业运维/运行信息.md#)、[数据曲线](cn.zh-CN/Flink SQL开发指南/作业运维/数据曲线.md#) 和[FailOver](cn.zh-CN/Flink SQL开发指南/作业运维/FailOver.md#)等实时计算作业运维内容。

-   监控报警

    监控报警章节为您介绍如何创建和启动报警规则，详情请参见[监控报警](cn.zh-CN/Flink SQL开发指南/监控报警.md#)。

-   配置调优

    配置调优章节为您介绍Flink SQL作业的调优功能，主要包括[高性能Flink SQL优化技巧](cn.zh-CN/Flink SQL开发指南/配置调优/高性能Flink SQL优化技巧.md#)、[AutoConf自动配置调优](cn.zh-CN/Flink SQL开发指南/配置调优/AutoConf自动配置调优.md#)、[AutoScale自动配置调试](cn.zh-CN/Flink SQL开发指南/配置调优/AutoScale自动配置调试.md#)和[手动配置调优](cn.zh-CN/Flink SQL开发指南/配置调优/手动配置调优.md#)。

-   Flink SQL

    Flink SQL章节为您介绍Flink SQL语法，详情请参见[Flink SQL概述](cn.zh-CN/Flink SQL开发指南/Flink SQL/Flink SQL概述.md#)。


