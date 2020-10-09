# INSTR

This topic describes how to use the string function INSTR in Realtime Compute for Apache Flink.

**Note:** Only Blink 2.2.0 and later versions support the INSTR function.

## Syntax

```
INT instr( string1, string2 ) 
INT instr( string1, string2 [, start_position [, nth_appearance ] ] )   
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|string1|VARCHAR|The source string in which you search for the substring.|
|string2|VARCHAR|The substring that you want to search for in the source string.|
|start\_position|INT|The position in the source string where the search starts.-   This parameter is omitted by default. In this case, the index of the string starts from 1.
-   If the value of this parameter is positive, the search starts from left to right.
-   If the value of this parameter is negative, the search starts from right to left. |
|nth\_appearance|INT|The occurrence of the substring that you want to search for in the source string.-   This parameter is omitted by default. In this case, the first occurrence of the substring is searched for in the source string.
-   If the value of this parameter is negative, an error is reported. |

## Description

Returns the position of the substring in the source string. If the substring does not exist in the source string, the return value is 0.

## Example

-   Test data

    |string1\(VARCHAR\)|
    |------------------|
    |helloworld|

-   Test statements

    ```
    SELECT 
    instr('helloworld','lo') as res1,
    instr('helloworld','l',-1,1) as res2,   
    instr('helloworld','l',3,2) as res3
    FROM T1;            
    ```

-   Test results

    |res1\(INT\)|res2\(INT\)|res3\(INT\)|
    |-----------|-----------|-----------|
    |4|9|4|


