# MAX

This topic describes how to use the aggregate function MAX in Realtime Compute for Apache Flink. In Flink SQL, the MAX function returns the maximum value of all input values.

## Syntax

```
MAX(A)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|-   Supported data types: TINYINT, SMALLINT, INT, BIGINT, FLOAT, DECIMAL, DOUBLE, BOOLEAN, and VARCHAR.
-   Unsupported data types: DATE, TIME, TIMESTAMP, and VARBINARY. |

## Description

Returns the maximum value of all input values.

## Example

-   Test data

    |var1\(INT\)|
    |-----------|
    |4|
    |8|

-   Test statements

    ```
    SELECT MAX(var1) as aa
    FROM T1;
    ```

-   Test results

    |aa\(INT\)|
    |---------|
    |8|


