# TaskExecutor {#concept_62487_zh .concept}

本文为您介绍TaskExecutor在实时计算集群启动过程中的作用以及TaskExecutor的界面。

## 背景 {#section_uk2_scs_bgb .section}

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。TaskExecutor负责接收任务并将信息返还。TaskExecutor在启动时即完成了槽位数（Slot）的设置，每个Slot能启动1个Task线程。TaskExecutor从JobManager处接收需要部署的Task，部署启动后，与上游建立Netty连接，接收数据并处理。

## TaskExecutor在集群启动中的作用 {#section_44c_d61_6dq .section}

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。实时计算集群的启动流程如下：

1.  实时计算集群启动一个JobManger和若干个TaskExecutor。
2.  Client向JobManager提交任务。
3.  JobManager向TaskExecutor分配任务。
4.  TaskExecutor向JobManager汇报心跳和统计信息。

## TaskExecutor界面 {#section_xvx_wcs_bgb .section}

TaskExecutor界面为您提供Task列表以及Task详情的接口。

![taskexecutor](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41071/156396696034013_zh-CN.png)

