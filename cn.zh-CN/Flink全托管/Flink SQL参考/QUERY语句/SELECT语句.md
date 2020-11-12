# SELECT语句

SELECT语句用于从表中选取数据。

## 语法

```
SELECT [ DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression;
```

## 测试数据

|a（VARCHAR）|b（INT）|c（DATE）|
|----------|------|-------|
|a1|211|1990-02-20|
|b1|120|2018-05-12|
|c1|89|2010-06-14|
|a1|46|2016-04-05|

## 简单查询

-   测试语句

    ```
    SELECT * FROM 表名；
    ```

-   测试结果

    |a（VARCHAR）|b（INT）|c（DATE）|
    |----------|------|-------|
    |a1|211|1990-02-20|
    |b1|120|2018-05-12|
    |c1|89|2010-06-14|
    |a1|46|2016-04-05|


## 重命名查询

-   测试语句

    ```
    SELECT a, c AS d FROM 表名；
    ```

-   测试结果

    |a（VARCHAR）|d（DATE）|
    |----------|-------|
    |a1|1990-02-20|
    |b1|2018-05-12|
    |c1|2010-06-14|
    |a1|2016-04-05|


## 去重查询

-   测试语句

    ```
    SELECT DISTINCT a FROM 表名；
    ```

-   测试结果

    |a（VARCHAR）|
    |----------|
    |a1|
    |b1|
    |c1|


## 子查询

普通的SELECT是从几张表中读数据，例如`SELECT column_1, column_2 … FROM table_name`，但查询的对象也可以是另外一个SELECT操作。

**说明：** 当查询的对象是另一个SELECT操作时，必须为子查询加别名。

-   测试语句

    ```
    INSERT INTO result_table
    SELECT * FROM
                   (SELECT   t.a,
                             sum(t.b) AS sum_b
                    FROM     t1 t
                    GROUP BY t.a
                   ) t1 
    WHERE  t1.sum_b > 100;
    ```

-   测试结果

    |a（VARCHAR）|b（INT）|
    |----------|------|
    |a1|211|
    |b1|120|
    |a1|257|

    **说明：** 此结果为调试结果，会显示出计算过程。如果您的结果表是DataHub、消息队列Kafka或消息队列MQ等，正式上线也会显示过程数据。但如果您的结果表是云数据RDS等关系型数据库，正式上线，主键相同的记录显示为一条数据。


