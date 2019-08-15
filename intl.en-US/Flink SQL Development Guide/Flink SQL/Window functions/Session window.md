# Session window {#concept_uxv_qtv_cgb .concept}

This topic describes how to use the session window function of Realtime Compute.

## What is a session window {#section_esz_c5v_cgb .section}

A session window groups elements by session activity. Unlike tumbling and sliding windows, session windows do not overlap and are not fixed in size. If a session window does not receive any elements within a certain period, the session is disconnected and the window is closed.

A session window is configured by using a gap, which defines the length of an inactive period. For example, a data stream that represents a mouse click activity may include highly clustered mouse click events, separated with idle periods. If data arrives after the specified shortest gap, a new window is opened.

The following figure shows a session window. Note that different keys have different windows due to data distribution differences.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40914/156584843034336_en-US.png)

## Session window function syntax {#section_dhc_y1v_cgb .section}

The SESSION function is used to define a session window in a GROUP BY clause.

``` {#codeblock_8me_8d8_rxj .language-sql}
SESSION(<time-attr>, <gap-interval>)
<gap-interval>: INTERVAL 'string' timeUnit
			
```

**Note:** 

The <time-attr data-spm-anchor-id="a2762.11472859.0.i151.7ca4203bEk6mXa"\>`<time-attr>` parameter must be a valid time attribute in a stream to specify whether the time is the processing time or event time.</time-attr\> For more information about how to define the [time attribute](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#section_vhy_mp5_cgb) and [watermark](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#ul_pf2_sx5_cgb), see [Window function overview](reseller.en-US/Flink SQL Development Guide/Flink SQL/Window functions/Window function overview.md#).

## Window identifier functions {#section_dtr_rdv_cgb .section}

A window identifier function specifies the start or end time of a window, or the window time attribute for aggregation of lower-level windows.

|Window identifier function|Return type|Description|
|--------------------------|-----------|-----------|
|`SESSION_START(<time-attr>, <gap-interval>)`|TIMESTAMP|Return the start time \(border value\) of the window. For example, if the window is `[00:10, 00:15)`, `00:10` is returned.|
|`SESSION_END(<time-attr>, <gap-interval>)`|TIMESTAMP|Return the end time \(border value\) of the window. For example, if the window is `[00:00, 00:15)`, `00:15` is returned.|
|`SESSION_ROWTIME(<time-attr>, <gap-interval>)`|TIMESTAMP \(rowtime-attr\)|Return the end time \(not the border value\) of the window. For example, if the window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a rowtime attribute, based on which time type operations such as **window cascading** can be performed. The function is applicable only to windows based on event time.|
|`SESSION_PROCTIME(<time-attr>, <gap-interval>)`|TIMESTAMP \(rowtime-attr\)|Return the end time \(not the border value\) of the window. For example, if the window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a proctime attribute, based on which time type operations such as **window cascading** can be performed. The function is applicable only to windows based on processing time.|

## Example {#section_lpl_sdv_cgb .section}

The following example describes how to compute the number of clicks per user during each active session. The session timeout interval is 30 seconds.

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

    ``` {#codeblock_gvd_9sk_hr6 .language-SQL}
    CREATE TABLE user_clicks(
    username varchar,
    click_url varchar,
    ts timeStamp,
    WATERMARK wk FOR ts as withOffset(ts, 2000) -- Define a watermark for rowtime.
    ) with (
    type='datahub',
    ...
    );
    
    CREATE TABLE session_output(
    window_start TIMESTAMP,
    window_end TIMESTAMP,
    username VARCHAR,
    clicks BIGINT
    ) with (
    type='rds'
    );
    
    INSERT INTO session_output
    SELECT
    SESSION_START(ts, INTERVAL '30' SECOND),
    SESSION_END(ts, INTERVAL '30' SECOND),
    username,
    COUNT(click_url)
    FROM user_clicks
    GROUP BY SESSION(ts, INTERVAL '30' SECOND), username
    					
    ```

-   Test result

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2017-10-10 10:00:00.0`|`2017-10-10 10:00:40.0`|Jark|2|
    |`2017-10-10 10:00:49.0`|`2017-10-10 10:01:35.0`|Jark|2|
    |`2017-10-10 10:01:58.0`|`2017-10-10 10:02:28.0`|Jark|1|
    |`2017-10-10 10:02:10.0`|`2017-10-10 10:02:40.0`|Timo|1|


