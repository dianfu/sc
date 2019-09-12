# Watermark {#concept_lkg_hsy_bhb .concept}

实时计算可以基于时间属性对数据进行窗口聚合。基于的Event Time时间属性的窗口函数作业中，数据源表的声明中需要使用Watermark方法。

Watermark是一种衡量[Event Time](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#section_jf3_mhf_5fb)进展的机制，它是数据本身的一个隐藏属性，Watermark的定义是数据源表DDL定义的一部分。Flink提供了如下语法定义Watermark。

**说明：** 实时计算时间属性详情，请参见[时间属性](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#)。

``` {#codeblock_cm3_00a_ics .language-sql}
WATERMARK [watermarkName] FOR <rowtime_field> AS withOffset(<rowtime_field>, offset)
```

|参数|说明|
|--|--|
|watermarkName|标识Watermark的名字，可选。|
|<rowtime\_field\>|<rowtime\_field\>必须是表中已定义的一列（当前仅支持为TIMESTAMP类型），含义是基于该列生成Watermark，并且标识该列为Event Time列。您可以使用<rowtime\_field\>在作业代码中定义窗口。|
|withOffset|是目前提供的Watermark的生成策略，根据`<rowtime_field> - offset`生成Watermark的值。`withOffset`的第一个参数必须是<rowtime\_field\>。|
|offset|单位为毫秒，含义为Watermark值与Event Time值的偏移量。|

通常一条记录中的某个字段就代表了该记录的发生时间。例如，表中`rowtime`字段的类型为TIMESTAMP，`1501750584000（2017-08-03 08:56:24.000）`。定义一个基于该`rowtime`列，偏移4秒的Watermark的示例如下。

``` {#codeblock_qx1_snv_686}
WATERMARK FOR rowtime AS withOffset(rowtime, 4000)
```

这条数据的Watermark时间为 `1501750584000 - 4000 = 1501750580000（2017-08-03 08:56:20.000）`。这条数据的Watermark时间含义：时间戳小于`1501750580000（2017-08-03 08:56:20.000）`的数据已经全部到达。

**说明：** 

-   在使用Event Time Watermark时的`rowtime`必须是TIMESTAMP类型。当前支持毫秒级别的、在Unix时间戳里是13位。如果是其他类型或是在Unix时间戳不是13位，建议使用[计算列](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/计算列.md#)完成转换。
-   [Event Time](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#section_jf3_mhf_5fb)和[Processing Time](cn.zh-CN/Flink SQL开发指南/Flink SQL/基本概念/时间属性.md#section_lv4_5kf_5fb)的只能在源表上声明。

Watermark使用总结 

-   Watermark含义是所有时间戳`t'< t`的事件已经全部发生。若Watermark`t`已经生效，则后续Event Time小于`t`的记录将全部丢弃（后续支持用户配置，使Event Time小于`t`的数据也能继续更新）。
-   针对乱序的的流，Watermark至关重要。即使部分事件延迟到达，也不会过大影响窗口计算的正确性。
-   并行数据流中，当算子（Operator）有多个输入流时，算子的Event Time以最小流Event Time为准。

    ![](images/40782_zh-CN.svg)


