# IS\_ALPHA

本文为您介绍如何使用实时计算条件函数IS\_ALPHA。

## 语法

```
BOOLEAN IS_ALPHA(VARCHAR str) 
```

## 入参

|参数|数据类型|
|--|----|
|str|VARCHAR|

## 功能描述

如果str中只包含字母，则返回true，否则返回false。

## 示例

-   测试数据

    |e\(VARCHAR\)|f\(VARCHAR\)|g\(VARCHAR\)|
    |------------|------------|------------|
    |3|asd|null|

-   测试语句

    ```
    SELECT IS_ALPHA(e) as boo1,IS_ALPHA(f) as boo2,IS_ALPHA(g) as boo3
    FROM T1;             
    ```

-   测试结果

    |boo1\(BOOLEAN\)|boo2\(BOOLEAN\)|boo3\(BOOLEAN\)|
    |---------------|---------------|---------------|
    |false|true|false|


