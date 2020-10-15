# MONTH

本文为您介绍如何使用实时计算日期函数MONTH。

## 语法

```
BIGINT MONTH(TIMESTAMP timestamp) 
BIGINT MONTH(DATE date)
```

## 入参

|参数|数据类型|
|--|----|
|time|TIME|
|timestamp|TIMESTAMP|

## 功能描述

返回输入时间参数中的月，范围1～12。

## 示例

-   测试数据

    |a\(TIMESTAMP\)|b\(DATE\)|
    |--------------|---------|
    |2016-09-15 00:00:00|2017-10-15|

-   测试语句

    ```
    SELECT
     MONTH(cast( a as TIMESTAMP)) as int1,
     MONTH(cast( b as DATE)) as int2
    FROM T1;     
    ```

-   测试结果

    |int1\(BIGINT\)|int2\(BIGINT\)|
    |--------------|--------------|
    |9|10|


