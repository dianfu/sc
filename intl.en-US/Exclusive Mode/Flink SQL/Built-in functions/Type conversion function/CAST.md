# CAST

This topic describes how to use the type conversion function CAST in Realtime Compute for Apache Flink.

## Syntax

```
CAST(A AS type)    
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|For more information, see [Data type conversion](/intl.en-US/Exclusive Mode/Flink SQL/Data types/Data type conversion.md).|

## Description

Converts the value of the input parameter A to a specified type. If the type after conversion is not consistent with the field type in the destination table, `Insert into: Query result and target table 'test_result' field type(s) not match.` is reported.

## Example

-   Test data

    |var1\(VARCHAR\)|var2\(INT\)|
    |---------------|-----------|
    |1000|30|

-   Test statements

    ```
    SELECT CAST(var1 AS INT) as aa
    FROM T1;             
    ```

-   Test results

    |aa\(INT\)|
    |---------|
    |1000|


