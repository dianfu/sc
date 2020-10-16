# LAST\_VALUE

本文为您介绍如何使用实时计算聚合函数LAST\_VALUE。Flink SQL中使用LAST\_VALUE函数返回指定数据流的最后1条非NULL数据。

## 语法

```
T LAST_VALUE(T value)
T LAST_VALUE(T value，BIGINT order)
```

## 入参

|参数|数据类型|
|--|----|
|value|任意参数类型 **说明：** 所有输入参数需要为相同的数据类型。 |
|order|BIGINT|

## 功能描述

获取数据流的最后1条非NULL数据。根据ORDER判定LAST\_VALUE所在的行，取ORDER值最大的记录作为LAST\_VALUE。

## 示例1

-   测试数据

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|
    |-----------|--------|------------|
    |1L|1|“Hello”|
    |2L|2|“Hello”|
    |3L|3|“Hello”|
    |4L|4|“Hello”|
    |5L|5|“Hello”|
    |6L|6|“Hello”|
    |7L|7|“Hello World”|
    |8L|8|“Hello World”|
    |20L|20|“Hello World”|

-   测试语句

    ```
    SELECT c,
     LAST_VALUE(b)
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1;
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |“Hello”|1|
    |“Hello”|2|
    |“Hello”|3|
    |“Hello”|4|
    |“Hello”|5|
    |“Hello”|6|
    |“Hello World”|7|
    |“Hello World”|8|
    |“Hello World”|20|


## 示例2

-   测试数据

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|order \(BIGINT\)|
    |-----------|--------|------------|----------------|
    |1L|1|“Hello”|5|
    |2L|2|“Hello”|5|
    |3L|3|“Hello”|6|
    |4L|4|“Hello”|5|
    |5L|5|“Hello”|6|
    |6L|6|“Hello”|5|
    |7L|7|“Hello World”|4|
    |8L|8|“Hello World”|8|
    |20L|20|“Hello World”|9|

-   测试语句

    ```
    SELECT c,
     LAST_VALUE(b,order) 
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1;
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|3|
    |“Hello”|3|
    |“Hello”|3|
    |“Hello”|3|
    |“Hello World”|7|
    |“Hello World”|8|
    |“Hello World”|20|


