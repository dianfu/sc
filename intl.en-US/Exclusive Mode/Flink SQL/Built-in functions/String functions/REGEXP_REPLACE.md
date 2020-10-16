# REGEXP\_REPLACE

This topic describes how to use the string function REGEXP\_REPLACE in Realtime Compute for Apache Flink.

## Syntax

```
VARCHAR REGEXP_REPLACE(VARCHAR str, VARCHAR pattern, VARCHAR replacement)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The string.|
|pattern|VARCHAR|The substring to be replaced in the source string.|
|replacement|VARCHAR|The substring that is used to replace the substring that matches the regular expression.|

**Note:** The constants in the regular expression must comply with Java code standards. Codegen converts SQL string constants to Java code. Write the string `(\d)` as `'\d'` in the regular expression, which is in the same way you write a regular expression in Java.

## Description

Replaces a substring that matches a specified regular expression pattern in the source string with another substring, and returns a new string. If any input parameter is null or the regular expression is invalid, the return value is null.

## Example

-   Test data

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|replace1\(VARCHAR\)|
    |---------------|-------------------|-------------------|
    |2014-03-13|-|Empty string|
    |NULL|-|Empty string|
    |2014-03-13|-|null|
    |2014-03-13|Empty string|s|
    |2014-03-13|\(|s|
    |100-200|\(\\d+\)|num|

-   Test statements

    ```
    SELECT  REGEXP_REPLACE(str1, pattern1, replace1) as result
    FROM T1;
    ```

-   Test results

    |result\(VARCHAR\)|
    |-----------------|
    |20140313|
    |null|
    |null|
    |2014-03-13|
    |null|
    |num-num|


