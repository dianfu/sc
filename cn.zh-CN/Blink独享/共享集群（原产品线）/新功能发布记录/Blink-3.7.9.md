# Blink-3.7.9

本文为您介绍Blink 3.7.9版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

增加维表Partitioned Join校验。

**说明：**

-   对于普通Patitioned Join，如果上游是更新流，则上游Unique Key必须包含Shuffle Key。
-   对于Bucket Partitioned Join，上游不能是更新流。

## 主要缺陷修复

修复由于GeminiKeyedStateHandle没有注册FileSegmentStateHandle，导致Checkpoint文件被误删的缺陷。

