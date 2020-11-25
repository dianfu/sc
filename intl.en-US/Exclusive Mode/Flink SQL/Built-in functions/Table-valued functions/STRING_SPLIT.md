---
keyword: [STRING\_SPLIT, split]
---

# STRING\_SPLIT

This topic describes how to use the table-valued function STRING\_SPLIT in Realtime Compute for Apache Flink.

## Syntax

```
string_split(string, separator)
```

## Input parameters

|Parameter|Type|Description|
|---------|----|-----------|
|string|VARCHAR|The string that you want to split.|
|separator|VARCHAR|The specified delimiter.**Note:** The delimiter must be a single string. |

## Description

Splits a string into rows of substrings based on a specified delimiter and returns a table that consists of rows of substrings. Before you use this function, take note of the following items:

-   If the value of the string is null, this function returns an empty row.
-   If the string contains two or more consecutive delimiters, this function returns a zero-length substring.
-   If the string does not contain the specified delimiter, this function returns only this string.

## Example

-   Test data

    |d\(varchar\)|s\(varchar\)|
    |------------|------------|
    |abc-bcd|-|
    |hhh|-|

-   Test statements

    ```
    select d,v 
    from T1, 
    lateral table(string_split(d, s)) as T(v);
    ```

-   Test results

    |d\(varchar\)|v\(varchar\)|
    |------------|------------|
    |abc-bcd|abc|
    |abc-bcd|bcd|
    |hhh|hhh|


