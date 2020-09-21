# Blink-3.6.8

本文为您介绍Blink 3.6.8版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

-   优化时间序列数据库TSDB写入性能。
-   优化分析型数据库PostgreSQL写入性能。
-   调整表格存储Tablestore连接超时时间为20s。

## 主要缺陷修复

-   修复AutoScale运行期间出现作业失败无法恢复的问题。
-   修复分析型数据库MySQL版2.0结果表出现NullPointerException报错的问题。
-   修复使用RetracRank算子的作业出现ArrayIndexOutOfBoundsException报错的问题。
-   修复使用RetracRank算子的作业连续收到相同的retract消息时出现数据异常的问题。
-   修复Tablestore源表Tunnel全量转增量时出现数据丢失的问题。

