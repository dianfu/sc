# Checkpoints {#concept_62485_zh .concept}

阿里云实时计算提供可以恢复数据流，并和应用保持一致状态的容错机制。容错机制的核心是持续创建分布式数据流及其状态的快照。当系统出现故障时，这些快照充当可以回退的一致性检查点（Checkpoint）。

## 登录Checkpoints页面 {#section_ntj_tpv_soo .section}

1.  登录**作业运维**页面。
    1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
    2.  单击页面顶部的**运维**。
    3.  在**作业列表**区域，单击**作业名称**下的目标作业名。
2.  在**作业运维**页面，单击页面顶部的**Checkpoints**。

## Overview {#section_vdg_z53_eyp .section}

**说明：** 该功能仅适用于实时计算3.0及以上版本。

**Overview**为您展示最新的Checkpoint信息，包括各节点Checkpoint的进程、Duration、StateSize等信息。

## History {#section_kyo_gq5_rpa .section}

**说明：** 该功能仅适用于实时计算3.0及以上版本。

**History**为您展示近期Checkpoint的信息。单击行首的（+）可展现各节点Checkpoint的进程、Duration和StateSize等信息。

## Summary {#section_2ul_wt3_swr .section}

**说明：** 该功能仅适用于实时计算3.0及以上版本。

**Summary**为您展示已完成的Checkpoint的平均值、最大值和最小值信息。

## Configuration {#section_7ma_x3o_9pc .section}

**说明：** 该功能仅适用于实时计算3.0及以上版本。

**Configuration**为您展示Checkpoint的配置信息。

## Completed Checkpoints {#section_tbc_dwr_bgb .section}

**说明：** 该功能仅适用于实时计算3.0以下版本。

**Completed Checkpoints**为您展示已完成的Checkpoint信息。

|名称|详情描述|
|--|----|
|ID|Checkpoint的ID编号|
|Start Time|Checkpoint开始的时间|
|Durations \(ms\)|Checkpoint花费的时间|

## Task Latest Completed Checkpoint {#section_lrm_fwr_bgb .section}

**说明：** 该功能仅适用于实时计算3.0以下版本。

**Task Latest Completed Checkpoint**为您展示最新一次Checkpoint的详细信息。

|名称|详情描述|
|--|----|
|SubTask ID|SubTask的ID编号|
|State Size \(Bytes\)|Checkpoint的大小|
|Durations \(ms\)|Checkpoint的花费的时间|

