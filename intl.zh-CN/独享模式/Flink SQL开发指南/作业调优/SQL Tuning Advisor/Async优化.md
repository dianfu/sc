---
keyword: [Async, 吞吐率, CPU使用率]
---

# Async优化

您可以在维表JOIN时开启Async优化并配置相关参数，提高吞吐。

## 背景信息

维表JOIN默认为同步访问方式，上游进来一条数据，则系统去物理表中查询一次，等待返回后输出关联结果。因此网络等待时间极大地阻碍了吞吐和延迟。为了解决同步访问的问题，引入异步模式并发处理查询请求，从而连续的请求之间不需要阻塞等待。

Flink SQL基于Flink Async I/O和异步客户端实现了维表JOIN的异步化，极大地提高了吞吐率。同步模式和异步模式对比图如下。

![对比](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3714929951/p161580.png)

## 调优方式

在维表的DDL的WITH参数中添加async='true'，Async 相关参数如下。

|参数|说明|是否必填|备注|
|--|--|----|--|
|async|是否开启异步请求|否|默认值为fasle。|
|asyncResultOrder|异步结果顺序|否|取值如下：-   unordered（默认值）：无序。
-   ordered：有序。 |
|asyncTimeoutMs|异步请求的超时时间|否|单位毫秒，默认值为3分钟。|
|asyncCapacity|异步请求的队列容量|否|默认值为100。|
|asyncCallbackThreads|回调处理线程数|否|回调类中的onComplete和onError默认会在线程池中处理该线程池的大小，默认值为50。|
|asyncConnectionQueueMaxsize|最大请求发送数|否|当等待某个服务器返回结果的请求数量达到asyncConnectionQueueMaxsize值时，异步请求调用也会被阻塞，以防止客户端自身OOM（OutOfMemory），默认值为100。|
|asyncCallbackQueueMaxsize|最大回调处理队列|否|当等待回调处理的请求达到asyncCallbackQueueMaxsize值时，异步请求调用也会被阻塞，以防止客户端自身OOM，默认值为500。|

## 示例代码

```
CREATE TABLE dim_cn_item(
    rowkey VARCHAR,
    item_id VARCHAR,
    title VARCHAR,
    cate_id VARCHAR,
    cate_name VARCHAR,
    cate_level1_id VARCHAR,
    cate_level2_id VARCHAR,
    cate_level3_id VARCHAR,
    cate_level1_name VARCHAR,
    cate_level2_name VARCHAR,
    cate_level3_name VARCHAR,
    pinlei_id VARCHAR,
    pinlei_name VARCHAR,
    bu_id VARCHAR,
    bu_name VARCHAR,
    PRIMARY KEY(rowkey)
) WITH(
    type='alihbase',
    diamondKey = 'xxxx',
    diamondGroup ='yyyy',
    cacheTTLMs='3600000',
    async='true',
    cache='LRU',
    columnFamily='cf',
    cacheSize='1000',
    tableName='yourTableName'
);
```

## 常见问题

-   报错详情

    ```
    Caused by: org.apache.flink.table.api.TableException: Output mode can not be UNORDERED if the input is an update stream.
    at org.apache.flink.table.plan.util.TemporalJoinUtil$.validate(TemporalJoinUtil.scala:340)
    at org.apache.flink.table.plan.nodes.common.CommonTemporalTableJoin.translateToPlanInternal(CommonTemporalTableJoin.scala:144)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlanInternal(StreamExecTemporalTableJoin.scala:98)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlanInternal(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.org$apache$flink$table$plan$nodes$exec$StreamExecNode$$super$translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.exec.StreamExecNode$class.translateToPlan(StreamExecNode.scala:38)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlanInternal(StreamExecCalc.scala:89)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlanInternal(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.org$apache$flink$table$plan$nodes$exec$StreamExecNode$$super$translateToPlan(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.exec.StreamExecNode$class.translateToPlan(StreamExecNode.scala:38)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlan(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translate(StreamExecSink.scala:158)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translateToPlanInternal(StreamExecSink.scala:103)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translateToPlanInternal(StreamExecSink.scala:53)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at
    ```

-   报错原因

    上游数据为Update Stream，维表JOIN时Async模式未开启ordered。

    **说明：** Update Stream为TOPN或双流JOIN等。

-   解决方案

    在维表WITH参数中设置asyncResultOrder='ordered'。


