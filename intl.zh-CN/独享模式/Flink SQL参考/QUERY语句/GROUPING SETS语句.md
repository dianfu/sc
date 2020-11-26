# GROUPING SETS语句

如果您经常需要对数据进行多维度聚合分析（例如既需要按照a列聚合，也要按照b列聚合，同时要按照a和b两列聚合），您可以使用GROUPING SETS语句进行多维度聚合分析，避免多次使用UNION ALL影响性能。

## 语法格式

```
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
GROUP BY 
[GROUPING SETS { groupItem [, groupItem ]* } ];
```

## 示例

-   测试数据

    |username|month|day|
    |--------|-----|---|
    |Lily|10|1|
    |Lucy|11|21|
    |Lily|11|21|

-   测试案例

    ```
    SELECT  
        `month`,
        `day`,
        count(distinct `username`) as uv
    FROM tmall_item
    group by 
    grouping sets((`month`),(`month`,`day`));
    ```

-   测试结果

    |month|day|uv|
    |-----|---|--|
    |10|1|1|
    |10|null|1|
    |11|21|1|
    |11|null|1|
    |11|21|2|
    |11|null|2|

    **说明：** 此结果为调试结果，会显示出计算过程。如果您的结果表是DataHub、消息队列Kafka或消息队列MQ等，正式上线也会显示过程数据。但如果您的结果表是云数据RDS等关系型数据库，正式上线，主键相同的记录显示为一条数据。


