# MONTH

This topic describes how to use the date function MONTH in Realtime Compute for Apache Flink.

## Syntax

```
BIGINT MONTH(TIMESTAMP timestamp) 
BIGINT MONTH(DATE date)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|time|TIME|
|timestamp|TIMESTAMP|

## Description

Returns the month component of a specified time value. The return value ranges from 1 to 12.

## Example

-   Test data

    |a\(TIMESTAMP\)|b\(DATE\)|
    |--------------|---------|
    |2016-09-15 00:00:00|2017-10-15|

-   Test statements

    ```
    SELECT
     MONTH(cast( a as TIMESTAMP)) as int1,
     MONTH(cast( b as DATE)) as int2
    FROM T1;     
    ```

-   Test results

    |int1\(BIGINT\)|int2\(BIGINT\)|
    |--------------|--------------|
    |9|10|


