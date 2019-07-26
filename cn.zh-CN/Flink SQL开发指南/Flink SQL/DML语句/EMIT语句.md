# EMIT语句 {#concept_ejj_ppy_zfb .concept}

EMIT语法可以使QUERY根据不同场景，定义不同的输出策略，从而达到控制延迟或提高数据准确性的效果。

**说明：** EMIT语法仅支持实时计算`2.0`以上版本。

## 什么是EMIT策略 {#section_gwp_5py_zfb .section}

EMIT策略是指在Flink SQL中，QUERY根据不同场景选择不同的输出策略（例如最大延迟时长）。传统的ANSI SQL语法不支持这类输出策略。例如，1小时的时间窗口，窗口触发之前希望每分钟都能看到最新的结果，窗口触发之后希望不丢失迟到一天内的数据。针对这类场景，实时计算抽象出EMIT语法，并扩展到SQL语法。以下为不同场景下EMIT策略的示例：

-   窗口结束之前，按1分钟延迟输出，窗口结束之后无延迟输出。

    ``` {#codeblock_561_zit_cuf .language-SQL}
    EMIT 
      WITH DELAY '1'MINUTE BEFORE WATERMARK,
      WITHOUT DELAY AFTER WATERMARK
    ```

-   窗口结束之前不输出，窗口结束之后无延迟输出。

    ``` {#codeblock_uvt_ygp_x1h .language-SQL}
    EMIT WITHOUT DELAY AFTER WATERMARK
    ```

-   全局都按1分钟的延迟输出 \(minibatch\)。

    ``` {#codeblock_tyz_tka_ow9 .language-SQL}
    EMIT WITH DELAY '1'MINUTE
    ```

-   窗口结束之前按1分钟延迟输出。

    ``` {#codeblock_gdf_lxy_9v3 .language-SQL}
    EMIT WITH DELAY '1'MINUTE BEFORE WATERMARK 
    ```


## EMIT语法的用途 {#section_qb2_xpy_zfb .section}

EMIT语法能够实现以下2种功能：

-   控制延迟：针对窗口，设置窗口触发之前的EMIT输出频率，减少结果输出延迟。
-   提高数据精确性：不丢弃窗口触发之后的迟到的数据，修正输出结果。

**说明：** 选择EMIT策略时，请权衡业务复杂程度和资源消耗情况。越低的输出延迟、越高的数据精确性，需要提供越高的计算开销。

## EMIT语法 {#section_y5g_cqy_zfb .section}

EMIT语法是定义输出的策略，即定义在输出（INSERT INTO）中。当未配置EMIT语法时，保持原有默认行为，即WINDOW只在WATERMARK触发时EMIT一个结果。

**说明：** EMIT语句只能置于INSERT INTO语句中的所有QUERY语句之后，不能置于VIEW语句中。

``` {#codeblock_0gq_mjl_ibj}
INSERT INTO tableName
query
EMIT strategy [, strategy]*

strategy ::= {WITH DELAY timeInterval | WITHOUT DELAY} 
                [BEFORE WATERMARK |AFTER WATERMARK]

timeInterval ::='string' timeUnit            
```

|参数|说明|
|--|--|
|`WITH DELAY`|可延迟输出，即按指定时长进行间隔输出。|
|`WITHOUT DELAY`|不可以延迟输出，即每来一条数据就进行输出。|
|`BEFORE WATERMARK`|窗口结束之前的策略配置，即WATERMARK触发之前。|
|`AFTER WATERMARK`|窗口结束之后的策略配置，即WATERMARK触发之后。|

**说明：** 

-   其中`strategy`可以定义多个，同时定义BEFORE和BEFORE的策略。 但不能同时定义两个BEFORE或两个AFTER的策略。
-   若配置了AFTER WATERMARK策略，需要使用明文方式声明 `blink.state.ttl.ms` ，标识最大延迟时长。

## 生命周期 {#section_pct_dqy_zfb .section}

AFTER策略允许接收迟到的数据，窗口的状态（State）允许保留一定时长，等待迟到的数据。这段保留的时长称为生命周期TTL。运用AFTRE策略后，通过明文声明 `blink.state.ttl.ms`参数，您可以设置状态允许的生命周期。例如， `blink.state.ttl.ms=3600000` 表示状态允许保留超时时长为1小时内的数据，超时时长大于1小时的数据不被录入状态。

## EMIT语法示例 {#section_v4g_tli_jae .section}

窗口区间为1小时的滚动窗口 `tumble_window`的语法示例如下。

``` {#codeblock_wp6_kca_ef3}
CREATE VIEW tumble_window AS
SELECT 
  id,
  TUMBLE_START(rowtime, INTERVAL '1' HOUR) as start_time,
  COUNT(*) as cnt
FROM source
GROUP BY id, TUMBLE(rowtime, INTERVAL '1' HOUR)                    
```

默认 `tumble_window` 的输出需要等到1小时结束才能显示。如果您需要尽早看到窗口的结果（即使是不完整的结果），例如每分钟看到最新的窗口结果，可以添加如下语句：

``` {#codeblock_ded_k67_njc}
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK -- 窗口结束之前，每隔1分钟输出一次更新结果。                    
```

默认 `tumble_window` 会忽略并丢弃窗口结束后到达的数据，如果您需要将窗口结束后1天到达的数据统计进入结果 ，并且需要每接收1条数据后立刻更新结果，可以添加如下语句：

``` {#codeblock_6rs_kb8_8uq}
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK, 
     WITHOUT DELAY AFTER WATERMARK  -- 窗口结束后，每收到一条数据输出一次更新结果。

blink.state.ttl.ms = 86400000    --增加1天状态生命周期的配置。
```

## DELAY概念 {#section_qnh_3qy_zfb .section}

EMIT策略中的`DELAY`指的是用户可接受的数据延迟时长，该延迟是指从用户的数据从进入实时计算，到看到结果数据（从实时计算系统输出）的时间（Event Time或Processing Time）。延迟的计算基于系统时间。动态表（流式数据在实时计算内部的存储）中的数据发生变化的时间和结果表（实时计算外部的存储）中显示新记录的时间的间隔，称为延迟。

假设，实时计算系统的处理耗时是0，则在流式数据积攒的过程和Window等待窗口数据的过程可能会导致延迟。如果，用户指定了最多延迟30秒，则30秒可用于流式数据的积攒。如果Query是1小时的窗口，则最多延迟30秒的含义是每隔30秒更新结果数据。

-   配置`EMIT WITH DELAY '1' MINUTE` 

    如果是Group By聚合，则会用1分钟积攒流式数据。如果有Window且Window的Size大于1分钟，则Window会每隔1分钟更新一次结果数据，否则忽略这个配置，因为窗口依靠Watermark的输出就能保证这个Latency SLA。

-   配置`EMIT WITHOUT DELAY` 

    如果是Group By函数，则不会启用minibatch，每来一条数据都触发计算和输出。如果是Window函数，则也是每来一条数据都触发计算和输出。


## 目前状态和未来计划 {#section_bvx_3qy_zfb .section}

目前状态和未来计划：

1.  目前EMIT策略只支持TUMBLE，HOP窗口，暂不支持SESSION窗口。
2.  目前一个Job若有多个输出，多个输出的EMIT需要定义成一样的策略。未来会支持不一样的策略。
3.  目前EMIT语法还不能用来配置minibatch的`allowLateness`，未来打算使用EMIT策略来声明`allowLateness`。

