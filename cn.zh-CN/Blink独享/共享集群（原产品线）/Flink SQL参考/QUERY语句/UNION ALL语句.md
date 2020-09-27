# UNION ALL语句

UNION ALL语句将两个流式数据合并。两个流式数据的字段必须完全一致，包括字段类型和字段顺序。

## 语法

```
select_statement
UNION ALL
select_statement;
```

**说明：** 实时计算Flink版同样支持`UNION`函数。`UNION ALL`允许重复值，`UNION`不允许重复值。在实时计算Flink版系统中，`UNION`相当于`UNION ALL+Distinct`，运行效率低，通常不推荐使用`UNION`。

## 示例

-   测试数据

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|

    |a（varchar）|b（bigint）|c（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|
    |test1|1|10|

-   测试语句

    ```
    SELECT
        a,
        sum(b) as d,
        sum(c) as e
    FROM 
        (SELECT * from test_source_union1
        UNION ALL
        SELECT * from test_source_union2
        UNION ALL
        SELECT * from test_source_union3
        )t
     GROUP BY a;      
    ```

-   测试结果

    |a（varchar）|d（bigint）|e（bigint）|
    |----------|---------|---------|
    |test1|1|10|
    |test2|2|20|
    |test1|2|20|
    |test1|3|30|
    |test2|4|40|
    |test1|4|40|

    **说明：** 此结果为调试结果，会显示出计算过程。如果您的结果表是DataHub、消息队列Kafka或消息队列MQ等，正式上线也会显示过程数据。但如果您的结果表是云数据RDS等关系型数据库，正式上线，主键相同的记录显示为一条数据。


