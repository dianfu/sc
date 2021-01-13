---
keyword: [Partitioned All Cache, 超大维表]
---

# Partitioned All Cache调优

您可以使用Partitioned All Cache调优方式解决超大维表使用Cache All策略在进行JOIN时，缓存无法加载维表全部数据的问题。

## 背景信息

维表JOIN时使用Cache All策略，默认每个进程都会加载全量的维表数据到缓存中。使用Cache All的情况下，维表JOIN节点配置的内存大小至少是维表内存大小的2倍。

当维表较大时，维表JOIN节点需要消耗大量的内存，另外作业GC会比较严重。甚至对于超大维表（超过1 TB），内存里无法加载全量维表数据。为了解决这个问题，引入Partitioned All Cache优化，上游数据按照Join Key进行Shuffle，每个进程上只需要加载所需的维表数据到缓存中。

开启PartitionedJoin优化可以减少内存开销。但由于上游数据需要按照Join Key进行Shuffle，则会引入额外的网络开销和CPU开销。因此以下场景不适合开启PartitionedJoin：

-   上游数据在JOIN Key上存在严重的数据倾斜，这种场景如果开启PartitionedJoin，则会因为数据倾斜导致慢节点。
-   维表数据较小，例如小于2 GB。这种场景如果开启PartitionedJoin，节约的内存开销和额外引入的网络开销和CPU开销相比，不划算。

## 调优方式

在维表的DDL的WITH参数中添加partitionedJoin = 'true'参数。

## 示例代码

```
 CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id), 
  PERIOD FOR SYSTEM_TIME --维表标识。
) with (
  type = 'odps',
  endPoint = 'your_end_point_name',
  project = 'your_project_name',
  tableName = 'your_table_name',
  accessId = 'your_access_id',
  accessKey = 'your_access_key'，
  `partition` = 'ds=20180905', 
  cache = 'ALL',
  partitionedJoin = 'true' -- 开启partitionedJoin。
);
```

