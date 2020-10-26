# Blink-3.4.4

本文为您介绍Blink 3.4.4版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

Blink-3.4.4版本支持MaxCompute源表监听新分区。在DDL语句中设置subscribeNewPartition参数，参数默认是false。当参数值是true时，不指定partition参数，会动态监听新分区。详情参见[创建全量MaxCompute源表](/intl.zh-CN/独享模式/Flink SQL参考/DDL语句/创建数据源表/创建全量MaxCompute源表.md)。

## 主要缺陷修复

RDS连接性问题：Blink使用Druid链接池链接RDS，优化Druid链接池链接RDS的方式，避免链接长时间不用导致作业的Failover。

