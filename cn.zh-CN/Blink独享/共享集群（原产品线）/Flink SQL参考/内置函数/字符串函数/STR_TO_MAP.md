# STR\_TO\_MAP

本文为您介绍如何使用实时计算字符串函数STR\_TO\_MAP。

## 语法

```
MAP STR_TO_MAP(VARCHAR text)
MAP STR_TO_MAP(VARCHAR text, VARCHAR listDelimiter, VARCHAR keyValueDelimiter) 
```

## 功能描述

使用listDelimiter将text分隔成K-V对，然后使用keyValueDelimiter分隔每个K-V对，组装成MAP返回。默认listDelimiter为（,）, keyValueDelimiter为（=）。

## 入参

|参数|数据类型|说明|
|--|----|--|
|text|VARCHAR|输入文本。|
|listDelimiter|VARCHAR|用来将text分隔成K-V对。默认为（ ,）。|
|keyValueDelimiter|VARCHAR|用来分隔每个key和value。默认为（ =）。|

**说明：** 这里的Delimiter使用的是Java的正则表达式，遇到特殊字符需要转义。

## 测试语句

```
SELECT
  STR_TO_MAP('k1=v1,k2=v2')['k1'] as a
FROM T1;
```

## 测试结果

|a\(VARCHAR\)|
|------------|
|v1|

