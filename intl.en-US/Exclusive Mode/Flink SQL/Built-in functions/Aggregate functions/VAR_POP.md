# VAR\_POP

This topic describes how to use the aggregate function VAR\_POP in Realtime Compute for Apache Flink. In Flink SQL, the VAR\_POP function returns the population variance of all input values in a specified expression.

## Syntax

```
T VAR_POP(T value)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|value|Numeric type, such as BIGINT or DOUBLE|

## Description

Returns the population variance of all input values.

## Example

-   Test data

    |a\(BIGINT\)|c\(VARCHAR\)|
    |-----------|------------|
    |2900|Hi|
    |2500|Hi|
    |2600|Hi|
    |3100|Hello|
    |11000|Hello|

-   Test statements

    ```
    SELECT 
    VAR_POP(a) as `result`,
    c
    FROM MyTable
    GROUP BY c;
    ```

-   Test results

    |result\(BIGINT\)|c|
    |----------------|--|
    |28889|Hi|
    |15602500|Hello|


