# WHERE语句

WHERE语句可用于对SELECT语句中的数据进行筛选。

## 语法

```
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
[ WHERE booleanExpression ];
```

下面的运算符可在WHERE语句中使用。

|操作符|描述|
|---|--|
|=|等于|
|<\>|不等于|
|\>|大于|
|\>=|大于等于|
|<|小于|
|<=|小于等于|

## 示例

-   测试数据

    |Address|City|
    |-------|----|
    |Oxford Street|Beijing|
    |Fifth Avenue|Beijing|
    |Changan Street|shanghai|

-   测试语句

    ```
    SELECT * FROM XXXX WHERE City='Beijing';
    ```

-   测试结果

    |Address|City|
    |-------|----|
    |Oxford Street|Beijing|
    |Fifth Avenue|Beijing|


