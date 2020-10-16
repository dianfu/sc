# SUM

This topic describes how to use the aggregate function SUM in Realtime Compute for Apache Flink. In Flink SQL, the SUM function returns the sum of all input values.

## Syntax

```
SUM(A)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|TINYINT, SMALLINT, INT, BIGINT, FLOAT, DECIMAL, or DOUBLE|

## Description

Returns the sum of all input values.

## Example

-   Test data

    |var1\(INT\)|
    |-----------|
    |4|
    |4|

-   Test statements

    ```
    SELECT sum(var1) as aa
    FROM T1;
    ```

-   Test results

    |aa\(INT\)|
    |---------|
    |8|


