---
keyword: [Cache, Cache策略]
---

# Cache优化

您可以在维表Join时开启Cache策略并配置相关参数，提高作业吞吐。

## 背景信息

实时计算Flink版支持LRU和ALL两种缓存策略，它们的调优原理如下：

-   LRU：缓存维表里的部分数据。您可以通过cache='LRU'开启LRU缓存优化，系统会为每个JoinTable节点创建一个LRU本地缓存。源表的每条数据都会触发系统在Cache中查找数据，如果存在则直接关联输出，减少了一次I/O请求。如果不存在，再发起查询请求去维表中查找，返回的结果会先存入缓存以备下次查询。

    为了防止缓存无限制增长，可以通过cacheSize调整缓存大小。为了定期更新维表数据，可以通过cacheTTLMs调整缓存的失效时间。cacheTTLMs作用于每条缓存数据上，也就是某条缓存数据在指定时间内没有被访问，则会从缓存中移除。

-   ALL：缓存维表里的所有数据。您可以通过cache='ALL'开启ALL缓存优化。系统会为JoinTable节点起一个异步线程去同步维表数据。在作业刚启动时，会先阻塞上游数据，直到缓存数据加载完毕，可以保证在处理上游数据时，维表数据已经被加载到缓存中。

    之后所有的维表查询请求都会通过Cache查询。如果在Cache中无法找到数据，则关联键不存在。在缓存失效后，重新加载维表数据到缓存中。重新加载维表的过程不会阻塞维表关联的处理流程。重新加载的维表数据暂时存放在临时内存中，等加载完毕再和原先的维表数据进行原子替换。

    因为几乎没有I/O请求，所以使用cache ALL的维表JOIN性能可以非常高。但是由于内存需要可以同时容纳两份维表数据，因此需要加大内存的配置。


**说明：** 如果您的业务场景要求每次维表查询请求必须发起一次查询，不可以使用缓存数据，请不要开启缓存策略。

## 调优方式

维表的DDL的WITH参数中添加cache='LRU'或cache='ALL'。Cache相关参数如下。

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|-   None（默认值）：无缓存。
-   LRU：需要配置相关参数：缓存大小（cacheSize）、缓存更新时间间隔（cacheTTLMs）和是否开启PartitionedJoin（partitionedJoin）。
-   ALL： 需要配置相关参数：缓存更新时间间隔（cacheTTLMs）、更新时间黑名单（cacheReloadTimeBlackList）和是否开启PartitionedJoin（partitionedJoin）。 |
|cacheSize|缓存大小|否|当缓存策略选择LRU时，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存失效时间，单位为毫秒|否|-   当缓存策略选择LRU时，可以设置缓存失效时间，默认不过期。
-   当缓存策略选择ALL时，缓存失效时间为缓存重新加载的间隔时间，默认不重新加载。 |
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|否|可选，默认空，格式为'2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'。 -   用逗号（,）来分隔多个黑名单。
-   用箭头（-\>）来分割黑名单的起始结束时间。 |
|partitionedJoin|是否开启PartitionedJoin。|否|默认情况下为false，表示不开启partitionedJoin。在开启PartitionedJoin优化时，主表会在关联维表前，先按照Join KEY进行Shuffle，这样做有以下优点： -   在缓存策略为LRU时，可以提高缓存命中率。
-   在缓存策略为ALL时，节省内存资源，因为每个并发只缓存自己并发所需要的数据。 |

## 示例代码

```
 CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id)
) with (
  type = 'odps',
  endPoint = 'your_end_point_name',
  project = 'your_project_name',
  tableName = 'your_table_name',
  accessId = 'your_access_id',
  accessKey = 'your_access_key'，
  `partition` = 'ds=20180905', 
  cache = 'ALL'
);
```

