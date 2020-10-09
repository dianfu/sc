# IS TRUE

本文为您介绍如何使用实时计算逻辑运算函数IS TURE。

## 语法

```
A IS TRUE
```

## 入参

|参数|数据类型|
|--|----|
|A|BOOLEAN|

## 功能描述

如果A是TRUE时，则返回TURE。如果A是FALSE时，则返回FALSE。

## 示例

-   测试数据

    |int1\(INT\)|int2\(INT\)|
    |-----------|-----------|
    |255|97|

-   测试语句

    ```
    SELECT int2 as aa
    FROM T1
    WHERE int1=255 IS TRUE;             
    ```

-   测试结果

    |aa\(int\)|
    |---------|
    |97|


