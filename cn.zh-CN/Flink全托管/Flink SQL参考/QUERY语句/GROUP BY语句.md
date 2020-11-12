# GROUP BY语句

GROUP BY语句用于根据一个或多个列对结果集进行分组。

## 语法

```
SELECT [ DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
[ GROUP BY { groupItem [, groupItem ]* } ];
```

## 示例

-   测试数据（T1）

    |Customer|OrderPrice|
    |--------|----------|
    |Bush|1000|
    |Carter|1600|
    |Bush|700|
    |Bush|300|
    |Adams|2000|
    |Carter|100|

-   测试语句

    ```
    SELECT Customer,SUM(OrderPrice) FROM T1
    GROUP BY Customer;
    ```

-   测试结果

    |Customer|SUM\(OrderPrice\)|
    |--------|-----------------|
    |Bush|2000|
    |Carter|1700|
    |Adams|2000|


