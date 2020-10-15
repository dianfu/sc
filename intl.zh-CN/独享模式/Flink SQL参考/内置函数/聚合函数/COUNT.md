# COUNT

本文为您介绍如何使用实时计算聚合函数COUNT。Flink SQL中使用COUNT函数返回输入列的数量。

## 语法

```
COUNT(A)
```

## 入参

|参数|数据类型|
|--|----|
|A|-   支持TINYINT、SMALLINT、INT、BIGINT、FLOAT、DECIMAL、DOUBLE、BOOLEAN和VARCHAR类型。
-   不支持DATE、TIME、TIMESTAMP和VARBINARY类型。 |

## 示例

-   测试数据

    |var1\(VARCHAR\)|
    |---------------|
    |1000|
    |100|
    |10|
    |1|

-   测试语句

    ```
    SELECT COUNT(var1) as aa
    FROM T1;     
    ```

-   测试结果

    |aa\(BIGINT\)|
    |------------|
    |4|


