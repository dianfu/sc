# Checkpoints {#concept_62485_zh .concept}

阿里云实时计算提供可以恢复数据流，并和应用保持一致状态的容错机制。容错机制的核心是持续创建分布式数据流及其状态的快照。当系统出现故障时，这些快照充当可以回退的一致性检查点（Checkpoint）。

![Checkpoints](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41069/156344349734002_zh-CN.png)

## Completed Checkpoints {#section_tbc_dwr_bgb .section}

**Completed Checkpoints**为您展示已完成的Checkpoint信息。

|名称|详情描述|
|--|----|
|ID|Checkpoint的ID编号|
|Start Time|Checkpoint开始的时间|
|Durations \(ms\)|Checkpoint花费的时间|

## Task Latest Completed Checkpoint {#section_lrm_fwr_bgb .section}

**Task Latest Completed Checkpoint**为您展示最新一次Checkpoint的详细信息。

|名称|详情描述|
|--|----|
|SubTask ID|SubTask的ID编号|
|State Size \(Bytes\)|Checkpoint的大小|
|Durations \(ms\)|Checkpoint的花费的时间|

