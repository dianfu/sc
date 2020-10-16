# REGEXP

本文为您介绍如何使用实时计算字符串函数REGEXP。

## 语法

```
BOOLEAN REGEXP(VARCHAR str, VARCHAR pattern) 
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|指定的匹配模式。|

## 功能描述

对指定的字符串执行一个正则表达式搜索，并返回一个Boolean值表示是否找到指定的匹配模式。如果str或者pattern为空或为NULL时，则返回false。

## 示例

-   测试数据

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|
    |---------------|-------------------|
    |k1=v1;k2=v2|k2\*|
    |k1:v1\|k2:v2|k3|
    |null|k3|
    |k1:v1\|k2:v2|null|
    |k1:v1\|k2:v2|\(|

-   测试语句

    ```
    SELECT  REGEXP(str1, pattern1) AS result
    FROM T1;              
    ```

-   测试结果

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |false|
    |false|
    |false|
    |false|


