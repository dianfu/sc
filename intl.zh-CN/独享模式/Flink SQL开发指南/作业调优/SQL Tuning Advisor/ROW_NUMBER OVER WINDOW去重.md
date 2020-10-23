---
keyword: [Deduplicate Keep LastRow, Deduplicate Keep FirstRow, ROW\_NUMBER OVER WINDOW]
---

# ROW\_NUMBER OVER WINDOW去重

您可以通过ROW\_NUMBER OVER WINDOW实现高效去重源数据。

## 背景信息

去重本质是一种特殊的TopN，实时计算Flink版支持以下两种去重策略：

-   保留首行的去重策略（Deduplicate Keep FirstRow）：保留KEY下第一条出现的数据，之后出现该KEY下的数据会被丢弃掉。因为STATE中只存储了KEY数据，所以性能较优。
-   保留末行的去重策略（Deduplicate Keep LastRow）：保留KEY下最后一条出现的数据。保留末行的去重策略性能略优于LAST\_VALUE函数。

## 调优方式

由于SQL中没有直接的去重语法，实时计算Flink版使用SQL的ROW\_NUMBER OVER WINDOW语法表示去重，语法格式如下。

```
SELECT *
FROM (
   SELECT *,
    ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
    ORDER BY timeAttributeCol [asc|desc]) AS rownum
   FROM table_name)
WHERE rownum = 1;
```

|参数|说明|
|--|--|
|ROW\_NUMBER\(\)|计算行号的OVER窗口函数。行号从1开始计算。|
|PARTITION BY col1\[, col2..\]|可选。指定分区的列，即去重的KEYS。|
|ORDER BY timeAttributeCol \[asc\|desc\]\)|指定排序的列，必须指定一个时间属性字段（Proctime或Rowtime）。还需要指定排列顺序（Deduplicate Keep FirstRow）或者倒序 （Deduplicate Keep LastRow）。**说明：**

-   如果不声明时间属性字段，默认为Proctime。
-   如果不声明排列顺序，默认为asc。 |
|rownum|仅支持rownum=1或rownum<=1。|

ROW\_NUMBER OVER WINDOW去重时需要执行两层查询：

1.  ROW\_NUMBER\(\)窗口函数根据时间属性列，对相同数据进行排序并标上排名。

    **说明：**

    -   当排序字段是Proctime列时，Flink就会按照系统时间去重，其每次运行的结果是不确定的。
    -   当排序字段是Rowtime列时，Flink就会按照业务时间去重，其每次运行的结果是确定的。
2.  对排名进行过滤，只取第一条或最后一条，达到去重目的。

## 代码示例

-   Deduplicate Keep FirstRow

    将T表按照b字段进行去重，并按照系统时间保留第一条数据。

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1;
    ```

-   Deduplicate Keep LastRow

    将T表按照b和d字段进行去重，并按照业务时间保留最后一条数据。

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1;
    ```


