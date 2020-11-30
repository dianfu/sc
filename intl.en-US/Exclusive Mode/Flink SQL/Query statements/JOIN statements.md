# JOIN statements

JOIN statements in Realtime Compute for Apache Flink have the same semantic meanings as those for batch processing. The two types of JOIN statements allow you to join two tables. The difference is that each JOIN statement in Realtime Compute for Apache Flink joins two dynamic tables. The join results are dynamically updated to ensure that the final results are the same as the corresponding results of batch processing.

## Syntax

```
tableReference [, tableReference ]* | tableexpression
[ LEFT ] JOIN tableexpression [ joinCondition ];
```

-   tableReference: specifies the table name.
-   tableexpression: specifies the expression.
-   joinCondition: specifies the join condition.

**Note:**

-   Only EQUI JOIN operations are supported.
-   Only INNER JOIN and LEFT OUTER JOIN operations are supported.

## Example 1: Join the Orders table and the Products table

-   Test data

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

-   Test statement

    ```
      SELECT o.rowtime, o.productId, o.orderId, o.units,p.name, p.unitPrice
      FROM Orders AS o
      JOIN Products AS p
      ON o.productId = p.productId;
    ```

-   Test result

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


## Example 2: Join the datahub\_stream1 table and the datahub\_stream2 table

-   Test data

    |a \(BIGINT\)|b \(BIGINT\)|c \(VARCHAR\)|
    |------------|------------|-------------|
    |0|10|test11|
    |1|10|test21|

    |a \(BIGINT\)|b \(BIGINT\)|c \(VARCHAR\)|
    |------------|------------|-------------|
    |0|10|test11|
    |1|10|test21|
    |0|10|test31|
    |1|10|test41|

-   Test statement

    ```
    SELECT s1.c,s2.c 
    FROM datahub_stream1 AS s1
    JOIN datahub_stream2 AS s2 
    ON s1.a =s2.a
    WHERE s1.a = 0;    
    ```

-   Test result

    |s1.c \(VARCHAR\)|s2.c \(VARCHAR\)|
    |----------------|----------------|
    |test11|test11|
    |test11|test31|


