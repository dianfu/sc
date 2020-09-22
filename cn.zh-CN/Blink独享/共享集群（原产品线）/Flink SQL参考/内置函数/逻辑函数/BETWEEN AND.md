# BETWEEN AND

本文为您介绍如何使用实时计算Flink版逻辑运算函数BETWEEN AND。

## 语法

```
A BETWEEN B AND C
```

## 入参

|参数|数据类型|
|--|----|
|A|DOUBLE，BIGINT，INT，VARCHAR，DATE，TIMESTAMP，TIME|
|B|DOUBLE，BIGINT，INT，VARCHAR，DATE，TIMESTAMP，TIME|
|C|DOUBLE，BIGINT，INT，VARCHAR，DATE，TIMESTAMP，TIME|

## 功能描述

BETWEEN操作符用于选取介于两个值之间的数据范围内的值。

## 示例1

-   测试数据

    |int1\(INT\)|int2\(INT\)|int3\(INT\)|
    |-----------|-----------|-----------|
    |90|80|100|
    |11|10|7|

-   测试语句

    ```
    SELECT int1 as aa
    FROM T1
    WHERE int1 BETWEEN int2 AND int3;
    ```

-   测试结果

    |aa\(int\)|
    |---------|
    |90|


## 示例2

-   测试数据

    |var1\(varchar\)|var2\(varchar\)|var3\(varchar\)|
    |---------------|---------------|---------------|
    |b|a|c|

-   测试语句

    ```
    SELECT var1 as aa
    FROM T1
    WHERE var1 BETWEEN var2 AND var3;      
    ```

-   测试结果

    |aa\(varchar\)|
    |-------------|
    |b|


## 示例3

-   测试数据

    |TIMESTAMP1\(TIMESTAMP\)|TIMESTAMP2\(TIMESTAMP\)|TIMESTAMP3\(TIMESTAMP\)|
    |-----------------------|-----------------------|-----------------------|
    |1969-07-20 20:17:30|1969-07-20 20:17:20|1969-07-20 20:17:45|

-   测试语句

    ```
    SELECT TIMESTAMP1 as aa
    FROM T1
    WHERE TIMESTAMP1 BETWEEN TIMESTAMP2 AND TIMESTAMP3;
    ```

-   测试结果

    |aa\(TIMESTAMP\)|
    |---------------|
    |1969-07-20 20:17:30|


