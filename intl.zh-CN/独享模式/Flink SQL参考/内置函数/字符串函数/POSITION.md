---
keyword: POSITION
---

# POSITION

本文为您介绍如何使用实时计算字符串函数POSITION。

## 语法

```
INTEGER POSITION( x IN  y)
```

## 入参

|参数|数据类型|
|--|----|
|x|VARCHAR|
|y|VARCHAR|

## 功能描述

返回目标字符串x在被查询字符串y里第一次出现的位置。如果目标字符串x在被查询字符串y中不存在，返回值为0。

## 示例

-   测试语句

    ```
    POSITION('in' IN 'china') as result
    FROM T1; 
    ```

-   测试结果

    |result\(INT\)|
    |-------------|
    |3|


