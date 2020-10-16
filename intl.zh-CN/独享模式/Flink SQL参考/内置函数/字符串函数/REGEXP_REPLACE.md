# REGEXP\_REPLACE

本文为您介绍如何使用实时计算字符串函数REGEXP\_REPLACE。

## 语法

```
VARCHAR REGEXP_REPLACE(VARCHAR str, VARCHAR pattern, VARCHAR replacement)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|被替换的字符串。|
|replacement|VARCHAR|用于替换的字符串。|

**说明：** 请您按照Java代码编写正则常量。Codegen会自动将SQL常量字符串转化为Java代码。描述一个数值`（\d）`的正则表达式和Java中一样，为 `'\d'`。

## 功能描述

用字符串`replacement`替换字符串`str`中正则模式为`pattern`的部分，并返回新的字符串。如果参数为NULL或者正则不合法时，则返回NULL。

## 示例

-   测试数据

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|replace1\(VARCHAR\)|
    |---------------|-------------------|-------------------|
    |2014-03-13|-|空|
    |NULL|-|空|
    |2014-03-13|-|NULL|
    |2014-03-13|空|s|
    |2014-03-13|\(|s|
    |100-200|\(\\d+\)|num|

-   测试语句

    ```
    SELECT  REGEXP_REPLACE(str1, pattern1, replace1) as result
    FROM T1;
    ```

-   测试结果

    |result\(VARCHAR\)|
    |-----------------|
    |20140313|
    |null|
    |null|
    |2014-03-13|
    |null|
    |num-num|


