# 创建MaxCompute维表 {#concept_dkd_xxh_mgb .concept}

本文为您介绍如何创建实时计算MaxCompute维表。

**说明：** 

-   推荐实时计算2.1.1及以上版本使用。
-   维表的Query语法参见： [维表JOIN语句](cn.zh-CN/Flink SQL开发指南/Flink SQL/QUERY语句/维表JOIN语句.md#)。
-   使用MaxCompute表作为维表，要先赋权读权限给MaxCompute账号。

## 创建MaxCompute维表示例 {#section_vhn_ryh_mgb .section}

``` {#codeblock_fst_906_zpn .language-sql}
CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id), 
  PERIOD FOR SYSTEM_TIME --定义了维表的标识。
) WITH (
  type = 'odps',
  endPoint = 'http://service.cn.maxcompute.aliyun-inc.com/api',
  project = '<projectName>',
  tableName = '<tableName>',
  accessId = '<yourAccessKeyId>',
  accessKey = '<yourAccessKeySecret>',
  `partition` = 'ds=2018****',
  cache = 'ALL'
)           
```

**说明：** 

-   声明维表时，必须要指名主键，维表JOIN的时候，ON的条件必须包含所有主键的等值条件。
-   MaxCompute维表主键必须有唯一性，否则会被去重。
-   `parition`是关键字，使用时需要使用反引号注释``partition``。
-   如果是分区表，目前不支持将分区列写入到schema定义中。
-   实时计算2.2.0及以上版本支持加载最新分区表，配置方法为`partition='max_pt()'`，`max_pt()`为所有分区的字典序最大值。

## WITH参数 {#section_glb_mqf_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|endPoint|ODPS服务地址|必选，参见[MaxCompute已开服的Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|可选，参见[MaxCompute已开服的Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。 **说明：** VPC环境下为必填。

 |
|project|ODPS项目名称|必选|
|tableName|表名|必选|
|accessId|accessId|必选|
|accessKey|accessKey|必选|
|partition|分区名|可选，分区表必填，具体分区信息到[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)查看。例如: 一个表的分区信息为`ds=20180905`，则可以写 ``partition` = 'ds=20180905'`。多级分区之间用逗号分隔，示例: ``partition` = 'ds=20180912,dt=xxxyyy'`|
|maxRowCount|可加载的最大表格数量|可选，默认为100000。 **说明：** 如果您的数据超过100000，需要设置maxRowCount参数。建议设定值比实际值大。

 |

## CACHE参数 {#section_b5c_qzh_mgb .section}

|参数|注释说明|备注|
|--|----|--|
|cache|缓存策略|仅支持`ALL`策略。|
|cacheSize|缓存大小|可以设置缓存大小，ODPS默认缓存值为100000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|当选择 `ALL`策略，则为缓存Reload的间隔时间，默认为不重新加载。|
|cacheReloadTimeBlackList|ALL Cache时启用，更新时间黑名单，防止在此时间内做cache更新（如双11场景）。|可选，默认为空，格式为`2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`。用逗号`,`来分隔多个黑名单，用箭头`->`来分割黑名单的起始结束时间。|
|cacheScanLimit|ALL Cache 时启用，读取全量HBase数据，服务端一次RPC返回给客户端的行数。|可选，默认为100条。|

目前ODPS维表只支持`ALL`全量缓存策略，必须显式声明。

ALL: 全量缓存策略。即在Job运行前会将远程表中所有数据load到内存中，之后所有的维表查询都会走cache，cache命中不到则不存在，并在缓存过期后重新加载一遍全量缓存。全量缓存策略适合远程表不大、miss key特别多的场景。全量缓存策略需要配置：缓存更新间隔（`cacheTTLMs`）和更新时间黑名单（`cacheReloadTimeBlackList`）。

**说明：** 注意：使用ALL Cache的时候，由于会进行异步Reload，需要将维表JOIN的节点增加内存，增加的内存大小为远程表两倍的数据量。

## METRIC {#section_jby_k23_mgb .section}

维表JOIN可以查看关联率，缓存命中率等METRIC信息。目前实时计算控制台还不支持查看，青铜狗通过Kmonitor查看。

|查询语句|说明|
|----|--|
|fetch qps|查询维表总qps，包括命中和不命中。对应的Metric Name：`blink.projectName.jobName.dimJoin.fetchQPS`。|
|fetchHitQPS|维表命中qps，包括换成命中和查询物理维表命中，对应Metric Name：`blink.projectName.jobName.dimJoin.fetchHitQPS`。|
|cacheHitQPS|维表缓存命中qps，对应Metric Name：`blink.projectName.jobName.dimJoin.cacheHitQPS`|
|dimJoin.fetchHit|维表关联率，对应Metric Name：`blink.projectName.jobName.dimJoin.fetchHit`。|
|dimJoin.cacheHit|维表缓存关联率，对应Metric Name：`blink.projectName.jobName.dimJoin.cacheHit`。|

## 如何查看MaxCompute分区名 {#section_nmv_tf3_mgb .section}

1.  进入[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)。
2.  搜索表名。
3.  在数据表详情界面的**明细信息** \> **分区信息**中进行查看。例如：[adm\_dim\_csn\_trans\_shift](https://meta.dw.alibaba-inc.com/store/table/table_detail.html?guid=odps.cndata.adm_dim_csn_trans_shift#/tableDetails/partitionInfo)的分区是`ds=20180905`

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/106707/156531421937584_zh-CN.png)

    。


## 常见问题 {#section_r5d_g33_mgb .section}

Q：任务出现了`RejectedExecutionException: Task java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTas`的Failover该怎么处理？

A：实时计算1.x版本中维表JOIN存在一定的问题，推荐升级到2.1.1及以上实时计算版本。如果需要继续使用原有版本，需要对作业进行暂停恢复操作，根据failover history中第一次出现failover的具体报错信息进行排查。

Q：MaxCompute中的数据类型和实时计算中数据类型的对应关系是什么？

A：如下表。

|MaxCompute|Blink|
|----------|-----|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|STRING|STRING|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|

**说明：** 其他MaxCompute类型，ODPS connector暂时还没有支持转换。

