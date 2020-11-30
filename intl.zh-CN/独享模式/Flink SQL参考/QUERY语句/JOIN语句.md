# JOIN语句

实时计算的JOIN和传统批处理JOIN的语义一致，都用于将两张表关联起来。区别为实时计算关联的是两张动态表，关联的结果也会动态更新，以保证最终结果和批处理结果一致。

## 语法

```
tableReference [, tableReference ]* | tableexpression
[ LEFT ] JOIN tableexpression [ joinCondition ];
```

-   tableReference：表名称。
-   tableexpression：表达式。
-   joinCondition：JOIN条件。

**说明：**

-   只支持等值连接，不支持非等值连接。
-   只支持INNER JOIN和LEFT OUTER JOIN两种JOIN方式。

## Orders JOIN Products表的数据示例

-   测试数据

    |rowtime|productId|orderId|units|
    |-------|---------|-------|-----|
    |`10:17:00`|30|5|4|
    |`10:17:05`|10|6|1|
    |`10:18:05`|20|7|2|
    |`10:18:07`|30|8|20|
    |`11:02:00`|10|9|6|
    |`11:04:00`|10|10|1|
    |`11:09:30`|40|11|12|
    |`11:24:11`|10|12|4|

    |productId|name|unitPrice|
    |---------|----|---------|
    |30|Cheese|`17`|
    |10|Beer|`0.25`|
    |20|Wine|`6`|
    |30|Cheese|`17`|
    |10|Beer|`0.25`|
    |10|Beer|`0.25`|
    |40|Bread|`100`|
    |10|Beer|`0.25`|

-   测试语句

    ```
      SELECT o.rowtime, o.productId, o.orderId, o.units,p.name, p.unitPrice
      FROM Orders AS o
      JOIN Products AS p
      ON o.productId = p.productId;
    ```

-   测试结果

    |o.rowtime|o.productId|o.orderId|o.units|p.name|p.unitPrice|
    |---------|-----------|---------|-------|------|-----------|
    |`10:17:00`|30|5|4|Cheese|`17`|
    |`10:17:05`|10|6|1|Beer|`0.25`|
    |`10:18:05`|20|7|2|Wine|`6`|
    |`10:18:07`|30|8|20|Cheese|`17`|
    |`11:02:00`|10|9|6|Beer|`0.25`|
    |`11:04:00`|10|10|1|Beer|`0.25`|
    |`11:09:30`|40|11|12|Bread|`100`|
    |`11:24:11`|10|12|4|Beer|`0.25`|


## datahub\_stream1 JOIN datahub\_stream2表的数据示例

-   测试数据

    |a（BIGINT）|b（BIGINT）|c（VARCHAR）|
    |---------|---------|----------|
    |0|10|test11|
    |1|10|test21|

    |a（BIGINT）|b（BIGINT）|c（VARCHAR）|
    |---------|---------|----------|
    |0|10|test11|
    |1|10|test21|
    |0|10|test31|
    |1|10|test41|

-   测试语句

    ```
    SELECT s1.c,s2.c 
    FROM datahub_stream1 AS s1
    JOIN datahub_stream2 AS s2 
    ON s1.a =s2.a
    WHERE s1.a = 0;    
    ```

-   测试结果

    |s1.c（VARCHAR）|s2.c（VARCHAR）|
    |-------------|-------------|
    |test11|test11|
    |test11|test31|


