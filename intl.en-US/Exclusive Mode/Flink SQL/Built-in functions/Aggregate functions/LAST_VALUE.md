# LAST\_VALUE

This topic describes how to use the aggregate function LAST\_VALUE in Realtime Compute for Apache Flink. In Flink SQL, the LAST\_VALUE function returns the last non-null record of a data stream.

## Syntax

```
T LAST_VALUE(T value)
T LAST_VALUE(T value, BIGINT order)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|value|Any data type **Note:** All input parameters must be of the same data type. |
|order|BIGINT|

## Description

Returns the last non-null record of a data stream. The row of LAST\_VALUE is determined based on the order parameter and the record with the largest order value is used as LAST\_VALUE.

## Example 1

-   Test data

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|
    |-----------|--------|------------|
    |1L|1|"Hello"|
    |2L|2|"Hello"|
    |3L|3|"Hello"|
    |4L|4|"Hello"|
    |5L|5|"Hello"|
    |6L|6|"Hello"|
    |7L|7|"Hello World"|
    |8L|8|"Hello World"|
    |20L|20|"Hello World"|

-   Test statements

    ```
    SELECT c,
     LAST_VALUE(b)
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1;
    ```

-   Test results

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |"Hello"|1|
    |"Hello"|2|
    |"Hello"|3|
    |"Hello"|4|
    |"Hello"|5|
    |"Hello"|6|
    |"Hello World"|7|
    |"Hello World"|8|
    |"Hello World"|20|


## Example 2

-   Test data

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|order \(BIGINT\)|
    |-----------|--------|------------|----------------|
    |1L|1|"Hello"|5|
    |2L|2|"Hello"|5|
    |3L|3|"Hello"|6|
    |4L|4|"Hello"|5|
    |5L|5|"Hello"|6|
    |6L|6|"Hello"|5|
    |7L|7|"Hello World"|4|
    |8L|8|"Hello World"|8|
    |20L|20|"Hello World"|9|

-   Test statements

    ```
    SELECT c,
     LAST_VALUE(b,order) 
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1;
    ```

-   Test results

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |"Hello"|1|
    |"Hello"|1|
    |"Hello"|3|
    |"Hello"|3|
    |"Hello"|3|
    |"Hello"|3|
    |"Hello World"|7|
    |"Hello World"|8|
    |"Hello World"|20|


