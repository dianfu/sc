# 高性能Flink SQL优化技巧 {#concept_xdd_nbz_zfb .concept}

本文为您介绍提升性能的Flink SQL推荐写法、推荐配置、推荐函数。

## Group Aggregate优化技巧 {#section_211_m3y_k5i .section}

-   开启MicroBatch/MiniBatch （提升吞吐）

    MicroBatch和MiniBatch都是微批处理，只是微批的触发机制略有不同。原理同样是缓存一定的数据后再触发处理，以减少对State的访问，从而提升吞吐和减少数据的输出量。

    MiniBatch主要依靠在每个Task上注册的Timer线程来触发微批，需要消耗一定的线程调度性能。MicroBatch是MiniBatch的升级版，主要基于事件消息来触发微批，事件消息会按您指定的时间间隔在源头插入。MicroBatch在元素序列化效率、反压表现、吞吐和延迟性能上都要优于胜于MiniBatch。

    -   适用场景

        微批处理是增加延迟来换取高吞吐的策略，如果您有超低延迟的要求，不建议开启微批处理。通常对于聚合的场景，微批处理可以显著的提升系统性能，建议开启。

        **说明：** MicroBatch模式也能解决两级聚合数据抖动问题。

    -   开启方式

        MicroBatch/MiniBatch默认关闭，开启方式：

        ``` {#codeblock_txe_lb2_eix}
        #3.2及以上版本开启Window miniBatch方法（3.2及以上版本默认不开启Window miniBatch。）
        sql.exec.mini-batch.window.enabled=true
        # 批量输出的间隔时间，使用microBatch策略时需要加上该配置，且建议和blink.miniBatch.allowLatencyMs保持一致。
        blink.microBatch.allowLatencyMs=5000
        # 使用microBatch时需要保留以下两个miniBatch配置。
        blink.miniBatch.allowLatencyMBs=5000
        # 防止OOM设置每个批次最多缓存数据的条数。
        blink.miniBatch.size=20000
        ```

-   开启LocalGlobal（解决常见数据热点问题）

    LocalGlobal优化即将原先的Aggregate分成Local+Global 两阶段聚合，也就是在MapReduce模型中熟知的Combine+Reduce处理模式。第一阶段在上游节点本地攒一批数据进行聚合（localAgg），并输出这次微批的增量值（Accumulator），第二阶段再将收到的Accumulator merge起来，得到最终的结果（globalAgg）。

    LocalGlobal本质上能够靠localAgg的聚合筛除部分倾斜数据，从而降低globalAgg的热点，从而提升性能。LocalGlobal如何解决数据倾斜问题可以结合下图理解。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75347/156326647533642_zh-CN.png)

    -   适用场景

        LocalGlobal适用于提升如SUM、COUNT、MAX、MIN和AVG等普通聚合的性能，以及解决这些场景下的数据热点问题。

        **说明：** 开启LocalGlobal需要UDAF实现M方法。

    -   开启方式

        在 实时计算`2.0`版本开始，LocalGlobal是默认开启的，参数是： `blink.localAgg.enabled=true`，但是需要在microbatch/minibatch开启的前提下才能生效。

    -   如何判断是否生效

        观察最终生成的拓扑图的节点名字中是否包含**GlobalGroupAggregate**或**LocalGroupAggregate**

-   开启PartialFinal（解决COUNT DISTINCT热点）

    上述的LocalGlobal优化能针对常见普通聚合有较好的效果（如SUM、COUNT、MAX、MIN和AVG）。但是对于COUNT DISTINCT收效不明显，原因是COUNT DISTINCT在local聚合时，对于DISTINCT KEY的去重率不高，导致在Global节点仍然存在热点。

    实时计算历史版本用户为了解决COUNT DISTINCT的热点问题时，通常会手动改写成两层聚合（增加按distinct key 取模的打散层），自`2.2.0`版本开始，实时计算提供了COUNT DISTINCT自动打散，我们称之为PartialFinal优化，您无需自己改写成两层聚合。PartialFinal和LocalGlobal的原理对比请参见下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75347/156326647533643_zh-CN.png)

    -   适用场景

        使用COUNT DISTINCT且聚合节点性能无法满足时。

        **说明：** 

        -   PartialFinal优化方法不能在包含UDAF的Flink SQL中使用。
        -   数据量不大的情况下不建议使用PartialFinal优化方法。PartialFinal优化会自动打散成两层聚合，引入额外的网络Shuffle，在数据量不大的情况下，可能反而会浪费资源。
    -   开启方式

        默认不开启，使用参数显式开启`blink.partialAgg.enabled=true`

    -   如何判断是否生效

        观察最终生成的拓扑图的节点名中是否包含**Expand**节点，或者原来一层的聚合变成了两层的聚合。

-   改写成AGG WITH FILTER语法（提升大量COUNT DISTINCT场景性能）

    **说明：** 仅支持实时计算`2.2.2` 及以上版本。

    统计作业需要计算各种维度的UV，比如全网UV、来自手淘的UV、来自PC的UV等等。建议使用更标准的AGG WITH FILTER语法来代替CASE WHEN实现多维度统计的功能。实时计算目前的SQL优化器能分析出Filter 参数，从而同一个字段上计算不同条件下的COUNT DISTINCT能共享State，减少对State的读写操作。性能测试中，使用AGG WITH FILTER语法来代替CASE WHEN能够能够使性能提高1倍。

    -   适用场景

        我建议用户将AGG WITH CASE WHEN的语法都替换成AGG WITH FILTER的语法，尤其是对同一个字段上计算不同条件下的COUNT DISTINCT结果时有极大的性能提升。

    -   原始写法

        ``` {#codeblock_thi_rla_aam .language-java}
        COUNT(distinct visitor_id)as UV1,COUNT(distinctcasewhen is_wireless='y'then visitor_id elsenullend)as UV2
        ```

    -   优化写法

        ``` {#codeblock_29o_z67_b5d .language-java}
        COUNT(distinct visitor_id)as UV1,COUNT(distinct visitor_id) filter (where is_wireless='y')as UV2
        ```


## TopN优化技巧 {#section_b0v_rga_gqc .section}

-   TopN算法

    当TopN的输入是非更新流（如Source），TopN只有一种算法AppendRank。当TopN的输入是更新流时（如经过了AGG/JOIN计算），TopN有3种算法，性能从高到低分别是：UpdateFastRank \>\> UnaryUpdateRank \>\> RetractRank。算法名字会显示在拓扑图的节点名字上。其中：

    -   UpdateFastRank ：最优算法，需要具备2个条件：1.输入流有PK信息。2.排序字段的更新是单调的，且单调方向与排序方向相反。例如，order by count/count\_distinct/sum（正数） desc。

        **说明：** order by sum（正数）desc时，要加上正数的过滤条件。且已知sum的参数不可能有负数，那么需要加上过滤条件从而告诉优化器这个信息，才能优化出UpdateFastRank算法（仅支持实时计算`2.2.2`及以上版本），如下所示。

        ``` {#codeblock_d1g_5k1_1pv .language-java}
        SELECT cate_id, seller_id, stat_date, pay_ord_amt  # 不输出rownum字段，能减小对结果表的输出量。
        FROM (SELECT*
              ROW_NUMBER ()OVER(PARTITIONBY cate_id, stat_date   # 注意要有时间字段，否则state过期会导致数据错乱。
        ORDERBY pay_ord_amt DESC## 根据上游sum结果排序)AS rownum。
          FROM (SELECT cate_id, seller_id, stat_date,# 重点。声明sum的参数都是正数，所以sum的结果是单调递增的，所以TopN能用优化算法。
        sum(total_fee) filter (where total_fee >=0)as pay_ord_amt
            FROM WHERE total_fee >=0GROUPBY cate_name, seller_id, stat_date)WHERE rownum <=100))
        ```

    -   UnaryUpdateRank：仅次于UpdateFastRank的算法。需要具备1个条件：输入流存在PK信息。例如，order by avg。
    -   RetractRank：普通算法，性能最差，不建议在生产环境使用该算法。请检查输入流是否存在PK信息，如果存在可进行UnaryUpdateRank或UpdateFastRank优化。
-   TopN优化方法
    -   无排名优化

        TopN的输出结果无需要显示rownum值，仅需在最终前端显式时进行1次排序，极大地减少输入结果表的数据量。 无排名优化方法详情请参见[TopN语句](cn.zh-CN/Flink SQL开发指南/Flink SQL/QUERY语句/TopN语句.md#)。

    -   增加TopN的cache大小

        TopN为了提升性能有一个state cache层，cache层能提升对state的访问效率。TopN的cache命中率的计算公式为：

        ``` {#codeblock_pzd_1om_3cc .language-java}
        cache_hit = cache_size*parallelism/top_n/partition_key_num
        ```

        例如，Top100配置缓存10000条，并发50，当您的patitionBy的key维度较大时，如10万级别时，cache命中率只有10000\*50/100/100000=5%，命中率会很低，导致大量的请求都会击中state（磁盘），性能会大幅下降。因此当partitionKey维度特别大时，可以适当加大TopN的cache size，相对应的也建议适当加大TopN节点的heap memory（参见[手动配置调优](cn.zh-CN/Flink SQL开发指南/配置调优/手动配置调优.md#)）。

        ``` {#codeblock_j41_lbi_h5u .language-java}
        ##默认10000条，调整TopN cahce到20万，那么理论命中率能达200000*50/100/100000 = 100%。
        blink.topn.cache.size=200000
        ```

    -   partitionBy的字段中要有时间类字段

        比如每天的排名，要带上day字段。否则TopN的结果到最后会由于state ttl有错乱。


## 高效去重方案 {#section_qfl_p2j_yak .section}

**说明：** 高效去重方案仅支持实时计算3.2.1及以上版本。

一些场景下，实时计算的源数据中存在重复数据。去重成为了用户经常反馈的需求。实时计算去重方案有保留第一条（Deduplicate Keep FirstRow）和保留最后一条（Deduplicate Keep LastRow）2种。

-   语法

    由于 SQL 上没有直接支持去重的语法，还要灵活的保留第一条或保留最后一条。因此我们使用了 SQL 的 ROW\_NUMBER OVER WINDOW 功能来实现去重语法。这非常像我们 TopN 支持的方案。实际上去重就是一种特殊的 TopN。

    ``` {#codeblock_xbu_z8a_6bl .language-java}
    SELECT *
    FROM (
       SELECT *,
        ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
         ORDER BY timeAttributeCol [asc|desc]) AS rownum
       FROM table_name)
    WHERE rownum = 1
    ```

    |参数|说明|
    |--|--|
    |ROW\_NUMBER\(\)|计算行号的OVER窗口函数。行号从1开始计算。|
    |PARTITION BY col1\[, col2..\]|可选。指定分区的列，即去重的KEYS。|
    |ORDER BY timeAttributeCol \[asc|desc\]\)|指定排序的列，必须是一个[时间属性](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#)的字段（即 proctime或rowtime）。可以指定顺序（Keep FirstRow）或者倒序 \(Keep LastRow\)。|
    |rownum|仅支持`rownum=1`或`rownum<=1`。|

    如上语法所示，去重需要两层Query：

    1.  使用ROW\_NUMBER\(\) 窗口函数来对数据根据时间属性列进行排序并标上排名。

        **说明：** 

        -   当排序字段是proctime列时，Flink就会按照系统时间去重，其每次运行的结果是不确定的。
        -   当排序字段是rowtime列时，Flink就会按照业务时间去重，其每次运行的结果是确定的。
    2.  对排名进行过滤，只取第一条，达到了去重的目的。

        **说明：** 排序方向可以是按照时间列顺序也可以倒。顺序并取第一条也就是Deduplicate Keep FirstRow，倒序并取第一条也就是Deduplicate Keep LastRow。

-   Deduplicate Keep FirstRow

    保留首行的去重策略：保留KEY下第一条出现的数据，之后出现该KEY下的数据会被丢弃掉。因为STATE中只存储了KEY数据，因此性能较优。示例如下。

    ``` {#codeblock_1zb_eds_55j .language-SQL}
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    **说明：** 以上示例是将T表按照b字段进行去重，并按照系统时间保留第一条数据。proctime在这里是源表T中的一个具有Processing Time属性的字段。如果您按照系统时间去重，也可以将proctime字段简化 PROCTIME\(\) 函数调用，可以省略proctime字段的声明。

-   Deduplicate Keep LastRow

    保留末行的去重策略：保留KEY下最后一条出现的数据。性能略优于LAST\_VALUE函数。示例如下。

    ``` {#codeblock_ozv_9m5_bf4 .language-SQL}
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    **说明：** 以上示例是将T表按照b和d字段进行去重，并按照业务时间保留最后一条数据。rowtime在这里是源表T中的一个具有Event Time属性的字段。


## 高效的内置函数 {#section_m66_ru0_adj .section}

-   使用内置函数替换自定义函数

    请尽量使用内置函数。在老版本时，由于内置函数不齐全，很多用户都用的三方包的自定义函数。在实时计算2.x 中，我们对内置函数做了很多的优化（主要是节省了序列化/反序列化、以及直接对 bytes 进行操作），但是自定义函数无法享受到这些优化。

-   KEY VALUE 函数使用单字符的分隔符

    KEY VALUE 的签名是：`KEYVALUE(content, keyValueSplit, keySplit, keyName)`，当keyValueSplit和KeySplit是单字符时，如`:`、`,`会使用优化的算法，会在二进制数据上直接寻找所需的keyName 的值，而不会将整个content做切分。性能约有30%提升。

-   多KEYVALUE场景使用MULTI\_KEYVALUE

    **说明：** 仅支持实时计算-`2.2.2`及以上版本

    如果在query中有对同一个content 做大量KEYVALUE 的操作，比如content中包含10个key-value对，希望把10个value 的值都取出来作为字段。用户经常会写10个KEY VALUE函数，那么就会对content 做10次解析。在这种场景建议使用 [MULTI\_KEYVALUE](cn.zh-CN/Flink SQL开发指南/Flink SQL/内置函数/表值函数/MULTI_KEYVALUE.md#)，这是一个表值函数。使用该函数可以只对content 做一次 split 解析。性能约有 50%~100%的性能提升。

-   LIKE 操作注意事项
    -   如果需要进行startWith操作，使用`LIKE 'xxx%'`。
    -   如果需要进行endWith操作，使用`LIKE '%xxx'`。
    -   如果需要进行contains操作，使用`LIKE '%xxx%'`。
    -   如果需要进行equals操作，使用`LIKE 'xxx'`，等价于`str = 'xxx'`。
    -   如果需要匹配`_`字符，请注意要完成转义`LIKE '%seller/id%' ESCAPE '/'`。`_`在SQL中属于单字符通配符，能匹配任何字符。如果声明为`LIKE '%seller_id%'`，则不单会匹配`seller_id`还会匹配`seller#id`、`sellerxid`或`seller1id` 等，导致结果错误。
-   慎用正则函数（REGEXP）

    正则表达式是非常耗时的操作，对比加减乘除通常有百倍的性能开销，而且正则表达式在某些极端情况下[可能会进入无限循环](https://stackoverflow.com/questions/4500507/infinite-loop-in-regex-in-java)，导致作业卡住。建议使用LIKE。正则函数包括：

    -   [REGEXP](cn.zh-CN/Flink SQL开发指南/Flink SQL/内置函数/字符串函数/REGEXP.md#)
    -   [REGEXP\_EXTRACT](cn.zh-CN/Flink SQL开发指南/Flink SQL/内置函数/字符串函数/REGEXP_EXTRACT.md#)
    -   [REGEXP\_REPLACE](cn.zh-CN/Flink SQL开发指南/Flink SQL/内置函数/字符串函数/REGEXP_REPLACE.md#)

## 网络传输的优化 {#section_6rk_cy8_h47 .section}

目前常见的Partitioner 策略包括：

-   KeyGroup/Hash：根据指定的key分配。
-   Rebalance：轮询分配给各个channel。
-   Dynamic-Rebalance：根据下游负载情况动态选择分配给负载较低的channel。
-   Forward：未chain一起时，同Rebalance。chain一起时是一对一分配。
-   Rescale：上游与下游一对多或多对一。

-   使用Dynamic-Rebalance替代Rebalance

    Dynamic Rebalance，它可以根据当前各subpartition中堆积的buffer的数量，选择负载较轻的subpartition进行写入，从而实现动态的负载均衡。相比于静态的rebalance策略，在下游各任务计算能力不均衡时，可以使各任务相对负载更加均衡，从而提高整个作业的性能。例如，在使用rebalance时，发现下游各个并发负载不均衡时，可以考虑使用 Dynamic-Rebalance。参数：`task.dynamic.rebalance.enabled=true`， 默认关闭。

-   使用Rescale替代Rebalance

    **说明：** 仅支持实时计算`2.2.2`及以上版本。

    例如，上游是5个并发，下游是10个并发。当使用Rebalance时，上游每个并发会轮询发给下游10个并发。当使用Rescale时，上游每个并发只需轮询发给下游2个并发。因为 channel个数变少了，subpartition的buffer填充速度能变快，能提高网络效率。当上游的数据比较均匀时，且上下游的并发数成比例时，可以使用Rescale替换Rebalance。参数：`enable.rescale.shuffling=true`，默认关闭。


## 推荐的优化配置方案 {#section_ggn_k35_thn .section}

综上所述，作业建议使用如下的推荐配置：

``` {#codeblock_6gc_bm9_tus .language-java}
# EXACTLY_ONCE语义
blink.checkpoint.mode=EXACTLY_ONCE
# checkpoint间隔时间，单位毫秒。
blink.checkpoint.interval.ms=180000
blink.checkpoint.timeout.ms=600000
# 2.x使用niagara作为statebackend，以及设定state数据生命周期，单位毫秒。
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000
# 2.x开启5秒的microbatch（使用窗口函数不能设置该参数）。
blink.microBatch.allowLatencyMs=5000
# 整个Job允许的延迟。
blink.miniBatch.allowLatencyMs=5000
# 单个batch的size
blink.miniBatch.size=20000
# local 优化，2.x默认已经开启，1.6.4需手动开启。
blink.localAgg.enabled=true
# 2.x开启partial优化，解决COUNT DISTINCT热点。
blink.partialAgg.enabled=true
# union all优化。
blink.forbid.unionall.as.breakpoint.in.subsection.optimization=true
# object reuse优化，默认已开启。
#blink.object.reuse=true
# GC优化（SLS做源表不能设置该参数）
blink.job.option=-yD heartbeat.timeout=180000 -yD env.java.opts='-verbose:gc -XX:NewRatio=3 -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:ParallelGCThreads=4'
# 时区设置
blink.job.timeZone=Asia/Shanghai
```

