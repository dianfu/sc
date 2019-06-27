# 创建Redis维表 {#concept_762018 .concept}

本文为您介绍如何创建实时计算Redis维表。

**说明：** 本文仅适用于实时计算3.2.2及以上版本。

## DDL {#section_033_h6g_uap .section}

实时计算支持使用Redis作为数据存储结果表。Redis结果表DDL定义如下。

``` {#codeblock_pfr_aos_iyn .lanuage-sql}
CREATE TABLE white_list (
  id VARCHAR,
  name VARCHAR,
  PRIMARY KEY (id),  --Redis中的Row Key字段。
  PERIOD FOR SYSTEM_TIME --维表的标识。
) WITH (
  type = 'redis',
  host = '<yourHostName>',
  port = '<yourPort>',
  password = '<yourPassword>',
  dbNum = '<yourDatabaseNumber>'
)
```

**说明：** 

-   Redis维表必须声明且只能声明一个主键。
-   维表JOIN时，ON的条件必须包含所有主键的等值条件。
-   Redis维表仅支持声明两个字段，且字段类型必须为VARCHAR。

## WITH参数 {#section_l5f_xor_vqo .section}

|参数|注释书名|备注|
|--|----|--|
|host|Redis连接地址|无。|
|port|Redis连接端口|默认为6379。|
|dbNum|选择操作的数据库|默认为0。|
|password|Redis密码|默认为空，不进行权限验证。|

## CACHE参数 {#section_so8_ayu_w5h .section}

|参数|注释说明|备注|
|cache|缓存策略|默认 `None`, 可选 `LRU`, `ALL`。 -   None：无缓存。
-   LRU：最近使用策略缓存。需要配置cacheSize参数和cacheTTLMs参数。
-   ALL：全量缓存策略。

 |
|cacheSize|缓存大小|选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时长|默认缓存不超时，单位为毫秒。不同缓存策略下的功能如下： -   LRU：设置缓存失效的超时时长。
-   ALL：设置缓存Reload的间隔时长，默认不重新加载。

 |

## METRIC {#section_3tt_j72_onp .section}

维表JOIN可以查看关联率，缓存命中率等Metric信息。

**说明：** 目前仅支持通过Kmonitor查看Metric信息。

|查询语句|说明|metric name|
|----|--|-----------|
|fetch qps|查询维表总QPS，包括命中或不命中。|blink.projectName.jobName.dimJoin.fetchQPS|
|fetchHitQPS|维表命中QPS，包括缓存命中和查询物理维表命中。|blink.projectName.jobName.dimJoin.fetchHitQPS|
|cacheHitQPS|维表缓存命中QPS。|blink.projectName.jobName.dimJoin.cacheHitQPS|
|dimJoin.fetchHit|维表关联率。|blink.projectName.jobName.dimJoin.fetchHit|
|dimJoin.cacheHit|维表缓存关联率|blink.projectName.jobName.dimJoin.cacheHit|

