# JobManager {#concept_62486_zh .concept}

JobManager是实时计算集群启动的重要组成部分，您可以在JobManager页面查看JobManager的详细参数信息。

## 登录JobManager页面 {#section_ati_nbu_qr7 .section}

1.  登录**作业运维**页面。
    1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
    2.  单击页面顶部的**运维**。
    3.  在**作业列表**区域，单击**作业名称**下的目标作业名。
2.  在**作业运维**页面，单击页面顶部的**JobManager**。

## JobManager在集群启动中的作用 {#section_jzt_5yr_bgb .section}

JobManager是实时计算集群的启动过程不可或缺的一部分。实时计算集群的启动流程如下：

1.  实时计算集群启动一个JobManger和若干个TaskManager。
2.  Client向JobManager提交任务。
3.  JobManager向TaskManager分配任务。
4.  TaskManager向JobManager汇报心跳和统计信息。

## JobManager参数信息 {#section_mj1_fzr_bgb .section}

您可以在**JobManager** \> **Attempt List** 页面，单击**操作**字段下的**查看详情**，查看JobManager的详细信息。

![JobManager](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41070/156870721734011_zh-CN.png)

