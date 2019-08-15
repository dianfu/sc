# Tumbling window {#concept_62511_zh .concept}

This topic describes how to use the tumbling window function of Realtime Compute.

## What is a tumbling window {#section_oxt_d1v_cgb .section}

By using tumbling windows, you assign each element to a window with the specified size. Generally, tumbling windows are fixed in size and do not overlap each other. For example, if a 5-minute tumbling window is defined, an infinite data stream is divided by period into windows such as `[0:00, 0:05)`, `[0:05, 0:10)`, `[0:10, 0:15)`. The following figure shows a 30-second tumbling window.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40912/156584880734298_en-US.png)

## Syntax {#section_dhc_y1v_cgb .section}

The TUMBLE function is used to define a tumbling window in a GROUP BY clause.

``` {#codeblock_llm_lf2_nj6 .language-sql}
TUMBLE(<time-attr>, <size-interval>)
<size-interval>: INTERVAL 'string' timeUnit
			
```

**Note:** 

The <time-attr data-spm-anchor-id="a2762.11472859.0.i151.7ca4203bEk6mXa"\>`<time-attr>` parameter must be a valid time attribute in a stream to specify whether the time is the processing time or event time.</time-attr\> For more information about how to define the [time attribute](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#section_vhy_mp5_cgb) and [watermark](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#ul_pf2_sx5_cgb), see [Window function overview](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#).

## Window identifier functions {#section_dtr_rdv_cgb .section}

A window identifier function specifies the start or end time of a window, or the window time attribute for aggregation of lower-level windows.

|Window identifier function|Return type|Description|
|--------------------------|-----------|-----------|
|`TUMBLE_START(time-attr, size-interval)`|TIMESTAMP|Return the start time \(border value\) of the window. For example, if the window is `[00:10, 00:15)`, `00:10` is returned.|
|`TUMBLE_END(time-attr, size-interval)`|TIMESTAMP|Return the end time \(border value\) of the window. For example, if the window is `[00:00, 00:15]`, `00:15` is returned.|
|`TUMBLE_ROWTIME(time-attr, size-interval)`|TIMESTAMP \(rowtime-attr\)|Return the end time \(not the border value\) of the window. For example, if the window is `[00:00, 00:15]`, `00:14:59.999` is returned. The return value is a rowtime attribute, based on which time type operations such as window cascading can be performed.|

## Example {#section_lpl_sdv_cgb .section}

The following describes how to compute the number of clicks per user and minute on the specified website.

-   Test data

    |username \(VARCHAR\)|click\_url \(VARCHAR\)|ts \(TIMESTAMP\)|
    |--------------------|----------------------|----------------|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:00.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:10.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:49.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:01:05.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:01:58.0`|
    |Timo|`http://taobao.com/xxx`|`2017-10-10 10:02:10.0`|

-   Test statements

    ``` {#codeblock_47s_19a_858 .language-SQL}
    CREATE TABLE user_clicks(
    username varchar,
    click_url varchar,
    ts timeStamp,
    WATERMARK wk FOR ts as withOffset(ts, 2000) -- Define a watermark for rowtime.
    ) with (
    type='datahub',
    ...
    );
    
    CREATE TABLE tumble_output(
    window_start TIMESTAMP,
    window_end TIMESTAMP,
    username VARCHAR,
    clicks BIGINT
    ) with (
    type='RDS'
    );
    
    INSERT INTO tumble_output
    SELECT
    TUMBLE_START(ts, INTERVAL '1' MINUTE),
    TUMBLE_END(ts, INTERVAL '1' MINUTE),
    username,
    COUNT(click_url)
    FROM user_clicks
    GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE), username
    					
    ```

-   Test result

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2017-10-10 10:00:00.0`|`2017-10-10 10:01:00.0`|Jark|3|
    |`2017-10-10 10:01:00.0`|`2017-10-10 10:02:00.0`|Jark|2|
    |`2017-10-10 10:02:00.0`|`2017-10-10 10:03:00.0`|Timo|1|


