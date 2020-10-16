---
keyword: POSITION
---

# POSITION

This topic describes how to use the string function POSITION in Realtime Compute for Apache Flink.

## Syntax

```
INTEGER POSITION( x IN  y)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|x|VARCHAR|
|y|VARCHAR|

## Description

Returns the position where the substring x appears for the first time in the source string y. If the substring does not exist in the source string, the return value is 0.

## Example

-   Test statements

    ```
    POSITION('in' IN 'china') as result
    FROM T1; 
    ```

-   Test results

    |result\(INT\)|
    |-------------|
    |3|


