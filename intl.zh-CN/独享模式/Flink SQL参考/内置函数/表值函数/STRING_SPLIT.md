---
keyword: [STRING\_SPLIT, 分隔]
---

# STRING\_SPLIT

本文为您介绍如何使用实时计算表值函数STRING\_SPLIT。

## 语法

```
string_split(string, separator)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|string|VARCHAR|目标字符串|
|separator|VARCHAR|指定的分隔符**说明：** 分隔符separator暂不支持多字符串形式，只支持单个字符串形式。 |

## 功能描述

根据指定的分隔符将目标字符串拆分为子字符串行，返回子字符串的单列的表。需要注意以下几点：

-   如果目标字符串为NULL，则STRING\_SPLIT表值函数返回一个空行。
-   如果目标字符串包含两个或多个连续出现的分隔符字符时，则返回长度为零的空子字符串。
-   如果目标字符串未包含指定字符，则只返回目标字符串。

## 示例

-   测试数据

    |d\(varchar\)|s\(varchar\)|
    |------------|------------|
    |abc-bcd|-|
    |hhh|-|

-   测试语句

    ```
    select d,v 
    from T1, 
    lateral table(string_split(d, s)) as T(v);
    ```

-   测试结果

    |d\(varchar\)|v\(varchar\)|
    |------------|------------|
    |abc-bcd|abc|
    |abc-bcd|bcd|
    |hhh|hhh|


