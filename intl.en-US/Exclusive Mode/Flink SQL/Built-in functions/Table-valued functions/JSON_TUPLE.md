# JSON\_TUPLE

This topic describes how to use the table-valued function JSON\_TUPLE in Realtime Compute for Apache Flink.

## Syntax

```
JSON_TUPLE(str, path1, path2 ..., pathN)     
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The JSON string.|
|path1 to pathN|VARCHAR|A path string, which does not start with a dollar sign \(`$`\).|

## Description

Returns the value represented by each path string from the JSON string.

## Example

-   Test data

    |d\(VARCHAR\)|s\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|qwe3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|qwe2|

-   Test statements

    ```
    SELECT d, v 
    FROM T1, lateral table(JSON_TUPLE(d, 'qwe', s))
    AS T(v);   
    ```

-   Test results

    |d\(VARCHAR\)|v\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd4|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd5|


