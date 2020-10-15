# MAX

本文为您介绍如何使用实时计算聚合函数MAX。Flink SQL中使用MAX函数返回所有输入值的最大值。

## 语法

```
MAX(A)
```

## 入参

|参数|数据类型|
|--|----|
|A|-   支持TINYINT，SMALLINT、INT、BIGINT、FLOAT、DECIMAL、DOUBLE、BOOLEAN和VARCHAR类型。
-   不支持DATE、TIME、TIMESTAMP和VARBINARY类型。 |

## 功能描述

返回所有输入值的最大值。

## 示例

-   测试数据

    |var1\(INT\)|
    |-----------|
    |4|
    |8|

-   测试语句

    ```
    SELECT MAX(var1) as aa
    FROM T1;
    ```

-   测试结果

    |aa\(INT\)|
    |---------|
    |8|


