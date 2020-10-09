# INSTR

本文为您介绍如何使用实时计算Flink版字符串函数INSTR。

**说明：** 仅Blink 2.2.0及以上版本支持INSTR函数。

## 语法

```
INT instr( string1, string2 ) 
INT instr( string1, string2 [, start_position [, nth_appearance ] ] )   
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|string1|VARCHAR|源字符串，要在该字符串中查找string2。|
|string2|VARCHAR|目标字符串，string1中查找的字符串。|
|start\_position|INT|起始位置，表示在string1中开始查找的其实位置：-   该参数省略（默认）： 字符串索引从1开始。
-   该参数为正：从左到右开始检索。
-   该参数为负：从右到左开始检索。 |
|nth\_appearance|INT|匹配序号代表要查找第几次出现的string2：-   该参数省略（默认）：第1次出现。
-   该参数为负：系统报错。 |

## 功能描述

返回目标字符串在源字符串中的位置，如果在源字符串中未找到目标字符串，则返回0。

## 示例

-   测试数据

    |string1\(VARCHAR\)|
    |------------------|
    |helloworld|

-   测试语句

    ```
    SELECT 
    instr('helloworld','lo') as res1,
    instr('helloworld','l',-1,1) as res2,   
    instr('helloworld','l',3,2) as res3
    FROM T1;            
    ```

-   测试结果

    |res1\(INT\)|res2\(INT\)|res3\(INT\)|
    |-----------|-----------|-----------|
    |4|9|4|


