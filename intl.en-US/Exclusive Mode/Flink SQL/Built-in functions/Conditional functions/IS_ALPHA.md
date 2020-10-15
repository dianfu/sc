# IS\_ALPHA

This topic describes how to use the conditional function IS\_ALPHA in Realtime Compute for Apache Flink.

## Syntax

```
BOOLEAN IS_ALPHA(VARCHAR str) 
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|str|VARCHAR|

## Description

Checks whether the specified string contains only letters. If yes, the return value is true. If not, the return value is false.

## Example

-   Test data

    |e\(VARCHAR\)|f\(VARCHAR\)|g\(VARCHAR\)|
    |------------|------------|------------|
    |3|asd|null|

-   Test statements

    ```
    SELECT IS_ALPHA(e) as boo1,IS_ALPHA(f) as boo2,IS_ALPHA(g) as boo3
    FROM T1;             
    ```

-   Test results

    |boo1\(BOOLEAN\)|boo2\(BOOLEAN\)|boo3\(BOOLEAN\)|
    |---------------|---------------|---------------|
    |false|true|false|


