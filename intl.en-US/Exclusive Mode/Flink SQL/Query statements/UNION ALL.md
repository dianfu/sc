# UNION ALL

A UNION ALL clause is used to combine two data streams. The field types and sequences of the two data streams must be the same.

## Syntax

```
select_statement
UNION ALL
select_statement;
```

**Note:** Realtime Compute for Apache Flink also supports the `UNION` function. `UNION ALL` allows duplicate values and `UNION` does not allow duplicate values. In Realtime Compute for Apache Flink, `UNION` is equivalent to the combination of `UNION ALL and DISTINCT`. We recommend that you do not use `UNION` because its operating efficiency is low.

## Example

-   Test data

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |test1|1|10|

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |test1|1|10|
    |test2|2|20|

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |test1|1|10|
    |test2|2|20|
    |test1|1|10|

-   Sample code

    ```
    SELECT
        a,
        sum(b) as d,
        sum(c) as e
    FROM 
        (SELECT * from test_source_union1
        UNION ALL
        SELECT * from test_source_union2
        UNION ALL
        SELECT * from test_source_union3
        )t
     GROUP BY a;      
    ```

-   Test results

    |a \(VARCHAR\)|d \(BIGINT\)|e \(BIGINT\)|
    |-------------|------------|------------|
    |test1|1|10|
    |test2|2|20|
    |test1|2|20|
    |test1|3|30|
    |test2|4|40|
    |test1|4|40|

    **Note:** The preceding test results are debugging results. In these results, you can view the computing process. If your job is published and the result table is stored in DataHub, Alibaba Cloud Message Queue for Apache Kafka, or Alibaba Cloud Message Queue, the result data contains data about the computing process. If your job is published and the result table is stored in a relational database such as ApsaraDB RDS, the records that have the same primary key values are combined into one record.


