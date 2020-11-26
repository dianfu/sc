# TaskExecutor

本文为您介绍TaskExecutor在实时计算集群启动过程中的作用以及TaskExecutor的界面。

**说明：** 本文档仅适用于实时计算3.0以下版本。

## 背景

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。TaskExecutor负责接收任务并将信息返还。TaskExecutor在启动时即完成了槽位数（Slot）的设置，每个Slot只能启动1个Task线程。TaskExecutor从JobManager处接收需要部署的Task，部署启动后，与上游建立Netty连接，接收数据并处理。

## 登录TaskExecutor界面

1.  登录[实时计算控制台](https://stream-ap-southeast-3.console.aliyun.com)。
2.  单击界面顶部的**运维**。
3.  在**作业列表**区域，单击**作业名称**下的目标作业名。
4.  在**作业运维**界面，单击顶部的**TaskExecutor**。

## TaskExecutor在集群启动中的作用

TaskExecutor是实时计算集群的启动过程不可或缺的一部分。实时计算集群的启动流程如下：

1.  实时计算集群启动一个JobManger和若干个TaskExecutor。
2.  Client向JobManager提交任务。
3.  JobManager向TaskExecutor分配任务。
4.  TaskExecutor向JobManager汇报心跳和统计信息。

## TaskExecutor界面

TaskExecutor界面为您提供Task列表以及Task详情的接口。

![taskexecutor](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9321659951/p34013.png)

