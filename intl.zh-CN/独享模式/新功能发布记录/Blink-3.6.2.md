# Blink-3.6.2

本文为您介绍Blink 3.6.2版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

-   优化AutoScale功能：没有流量的作业会触发Scale Down，减少资源浪费。
-   优化资源Rescale。
-   优化SLS Connector解析数据的方法：使用FastLogGroup方法解析数据。
-   降低大规模作业拓扑的内存消耗。

## 主要缺陷修复

-   修复Blink无法消费表格存储Tablestore源表积压数据的缺陷。
-   修复维表Cache ALL多并发导致NullPointException的缺陷。
-   修复MaxCompute Sink动态分区无法正常提交数据的缺陷。

