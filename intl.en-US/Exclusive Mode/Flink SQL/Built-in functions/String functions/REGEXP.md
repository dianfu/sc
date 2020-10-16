# REGEXP

This topic describes how to use the string function REGEXP in Realtime Compute for Apache Flink.

## Syntax

```
BOOLEAN REGEXP(VARCHAR str, VARCHAR pattern) 
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The string.|
|pattern|VARCHAR|The regular expression pattern.|

## Description

Performs regular expression matching on a specified string to check whether it matches a specified pattern. If the string or pattern is empty or null, the return value is false.

## Example

-   Test data

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|
    |---------------|-------------------|
    |k1=v1;k2=v2|k2\*|
    |k1:v1\|k2:v2|k3|
    |null|k3|
    |k1:v1\|k2:v2|null|
    |k1:v1\|k2:v2|\(|

-   Test statements

    ```
    SELECT  REGEXP(str1, pattern1) AS result
    FROM T1;              
    ```

-   Test results

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |false|
    |false|
    |false|
    |false|


