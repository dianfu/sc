# CAST

本文为您介绍如何使用实时计算类型转换函数CAST。

## 语法

```
CAST(A AS type)    
```

## 入参

|参数|数据类型|
|--|----|
|A|请参见[类型转换](/intl.zh-CN/独享模式/Flink SQL参考/数据类型/类型转换.md)。|

## 功能描述

将A值转换为给定类型。如果转换后的类型和目标表字段类型不匹配时，会出现类似`Insert into: Query result and target table 'test_result' field type(s) not match.`的报错。

## 示例

-   测试数据

    |var1\(VARCHAR\)|var2\(INT\)|
    |---------------|-----------|
    |1000|30|

-   测试语句

    ```
    SELECT CAST(var1 AS INT) as aa
    FROM T1;             
    ```

-   测试结果

    |aa\(INT\)|
    |---------|
    |1000|


