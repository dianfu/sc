# IS DISTINCT FROM

本文为您介绍如何使用实时计算逻辑运算函数IS DISTINCT FROM。

## 语法

```
A IS DISTINCT FROM B
```

## 入参

|参数|数据类型|
|--|----|
|A|任意数据类型|
|B|任意数据类型|

## 功能描述

-   A和B的数据类型、值不完全相同则返回`TRUE`。
-   A和B的数据类型、值都相同返回`FALSE`。
-   将空值视为相同。

## 示例

-   测试数据

    |A\(int\)|B\(varchar\)|
    |--------|------------|
    |97|97|
    |null|sss|
    |null|null|

-   测试语句

    ```
    SELECT 
    A IS DISTINCT FROM B as 'result'
    FROM T1;
    ```

-   测试结果

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |true|
    |false|


