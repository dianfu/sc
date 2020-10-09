# IS DISTINCT FROM

This topic describes how to use the logical function IS DISTINCT FROM in Realtime Compute for Apache Flink.

## Syntax

```
A IS DISTINCT FROM B
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|Any data type|
|B|Any data type|

## Description

-   If the data types or values of A and B are different, `true` is returned.
-   If the data types and values of A and B are the same, `false` is returned.
-   If both A and B are null, false is returned even when their data types are different.

## Example

-   Test data

    |A\(int\)|B\(varchar\)|
    |--------|------------|
    |97|97|
    |null|sss|
    |null|null|

-   Test statements

    ```
    SELECT 
    A IS DISTINCT FROM B as 'result'
    FROM T1;
    ```

-   Test results

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |true|
    |false|


