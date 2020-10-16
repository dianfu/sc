# SUM

本文为您介绍如何使用实时计算聚合函数SUM。Flink SQL中使用SUM函数来返回所有输入值的数值之和。

## 语法

```
SUM(A)
```

## 入参

|参数|数据类型|
|--|----|
|A|TINYINT、SMALLINT、INT、BIGINT、FLOAT、DECIMAL和DOUBLE。|

## 功能描述

返回所有输入值的数值之和。

## 示例

-   测试数据

    |var1\(INT\)|
    |-----------|
    |4|
    |4|

-   测试语句

    ```
    SELECT sum(var1) as aa
    FROM T1;
    ```

-   测试结果

    |aa\(INT\)|
    |---------|
    |8|


