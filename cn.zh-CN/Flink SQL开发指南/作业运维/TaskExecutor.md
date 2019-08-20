# TaskExecutor {#concept_62487_zh .concept}

本文为您介绍TaskExecutor在实时计算集群启动过程中的作用以及TaskExecutor的界面。

**说明：** 本文档仅适用于实时计算3.0以下版本。

## 背景 {#section_uk2_scs_bgb .section}

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。TaskExecutor负责接收任务并将信息返还。TaskExecutor在启动时即完成了槽位数（Slot）的设置，每个Slot能启动1个Task线程。TaskExecutor从JobManager处接收需要部署的Task，部署启动后，与上游建立Netty连接，接收数据并处理。

## 登录TaskExecutor页面 {#section_rpr_gga_c1q .section}

1.  登录**作业运维**平台。登录步骤参见[登录作业运维平台](cn.zh-CN/Flink SQL开发指南/作业运维/登录作业运维平台.md#)。
2.  在**作业运维**页面，单击页面顶部的**TaskExecutor**。

## TaskExecutor在集群启动中的作用 {#section_44c_d61_6dq .section}

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。实时计算集群的启动流程如下：

1.  实时计算集群启动一个JobManger和若干个TaskExecutor。
2.  Client向JobManager提交任务。
3.  JobManager向TaskExecutor分配任务。
4.  TaskExecutor向JobManager汇报心跳和统计信息。

## TaskExecutor界面 {#section_xvx_wcs_bgb .section}

TaskExecutor界面为您提供Task列表以及Task详情的接口。

![taskexecutor](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41071/156627253834013_zh-CN.png)

