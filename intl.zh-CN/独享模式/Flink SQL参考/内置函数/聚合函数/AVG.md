# AVG

本文为您介绍如何使用实时计算聚合函数AVG。Flink SQL中使用AVG函数返回指定表达式中所有值的平均值。

## 语法

```
AVG(A)
```

## 入参

|参数|数据类型|
|--|----|
|A|TINYINT、SMALLINT、INT、BIGINT、FLOAT、DECIMAL和DOUBLE。|

## 功能描述

返回指定表达式中所有值的平均值。

## 示例

-   测试数据

    |var1\(INT\)|var2\(INT\)|
    |-----------|-----------|
    |4|30|
    |6|30|

-   测试语句

    ```
    SELECT AVG(var1) as aa
    FROM T1;     
    ```

-   测试结果

    |aa\(INT\)|
    |---------|
    |5|


