# JSON\_TUPLE

本文为您介绍如何使用实时计算Flink版表值函数JSON\_TUPLE。

**说明：** 仅Blink支持JSON\_TUPLE，Flink不支持JSON\_TUPLE。

## 语法

```
JSON_TUPLE(str, path1, path2 ..., pathN)     
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|JSON字符串|
|path1～pathN|VARCHAR|表示路径的字符串，前面不需要`$`。|

## 功能描述

从JSON字符串中取出各路径字符串所表示的值。

## 示例

-   测试数据

    |d\(VARCHAR\)|s\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|qwe3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|qwe2|

-   测试语句

    ```
    SELECT d, v 
    FROM T1, lateral table(JSON_TUPLE(d, 'qwe', s))
    AS T(v);   
    ```

-   测试结果

    |d\(VARCHAR\)|v\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd4|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd5|


