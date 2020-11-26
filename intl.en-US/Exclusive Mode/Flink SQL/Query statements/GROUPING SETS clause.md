# GROUPING SETS clause

This topic describes how to use the GROUPING SETS clause in a single SELECT statement to aggregate and analyze data from multiple dimensions. For example, you can use the GROUPING SETS clause to aggregate the data in Column a, Column b, and both columns. If you do not use the GROUPING SETS clause, you must execute multiple UNION ALL clauses for multi-dimensional data aggregation and analysis. In this case, the system performance is compromised.

## Syntax

```
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
GROUP BY 
[GROUPING SETS { groupItem [, groupItem ]* } ];
```

## Examples

-   Test data

    |username|month|day|
    |--------|-----|---|
    |Lily|10|1|
    |Lucy|11|21|
    |Lily|11|21|

-   Sample code

    ```
    SELECT  
        `month`,
        `day`,
        count(distinct `username`) as uv
    FROM tmall_item
    group by 
    grouping sets((`month`),(`month`,`day`));
    ```

-   Test results

    |month|day|uv|
    |-----|---|--|
    |10|1|1|
    |10|null|1|
    |11|21|1|
    |11|null|1|
    |11|21|2|
    |11|null|2|

    **Note:** The preceding test result is a debugging result. In the result, you can view the computing process. If your job is published and the result table is stored in DataHub, Alibaba Cloud Message Queue for Apache Kafka, or Alibaba Cloud Message Queue, the result data contains the data about the computing process. If your job is published and the result table is stored in a relational database such as ApsaraDB for RDS, the records that have the same primary key values are combined into one record.


