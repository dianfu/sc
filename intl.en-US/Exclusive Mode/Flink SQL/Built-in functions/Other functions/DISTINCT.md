# DISTINCT

This topic describes how to use the DISTINCT function in Realtime Compute for Apache Flink. The DISTINCT function is used in SELECT statements to remove duplicate query results.

## Syntax

```
SELECT DISTINCT expressions 
FROM tables
...
```

-   `DISTINCT` must be placed before expressions. When you use `DISTINCT` with other functions at the same time, you must place the DISTINCT function at the beginning of a statement, for example, `concat_agg(DISTINCT ',' ,device_id)`.
-   `expressions` can be one or more expressions, specific columns, or any other valid expressions such as functions.

## Example

-   Test statements

    The following example shows how to use DISTINCT in Flink SQL:

    ```
    CREATE TABLE distinct_tab_source(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
       type='random'
    ) ;
    
    CREATE TABLE distinct_tab_sink(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
        type = 'print'
    ) ;
    
    INSERT INTO distinct_tab_sink 
    SELECT DISTINCT FirstName, LastName -- Remove duplicate records based on the FirstName and LastName columns.
    FROM distinct_tab_source;
    ```

-   Test data

    |FirstName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   Test results

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5199758951/p37912.png)

    **Note:**

    -   The test data contains four records. `DISTINCT FirstName, LastName` removes one duplicate record that contains SUNS,SHENGRAN and returns three unique records.
    -   The `SUNS,HENGRAN` and `SUN,SHENGRAN` records are retained. This indicates that `DISTINCT FirstName, LastName` processes the FirstName and LastName columns separately, and does not concatenate them for deduplication.

## Alternative for DISTINCT

`GROUP BY` in SQL statements also provides a deduplication function similar to that of `DISTINCT`. The following example shows the syntax of `GROUP BY`:

```
SELECT expressions 
FROM tables
GROUP BY expressions 
;        
```

The following example writes an SQL multi-insert query that has an equivalent effect to that of the DISTINCT function:

```
CREATE TABLE distinct_tab_source(
    FirstName  VARCHAR,
    LastName VARCHAR
)WITH(
   type='random'
);

CREATE TABLE distinct_tab_sink(
    FirstName  VARCHAR,
    LastName VARCHAR
)WITH(
    type = 'print'
);

CREATE TABLE distinct_tab_sink2(
    FirstName  VARCHAR,
    LastName VARCHAR
)WITH(
    type = 'print'
);

INSERT INTO distinct_tab_sink 
    SELECT DISTINCT FirstName, LastName -- Remove duplicate records based on the FirstName and LastName columns.
        FROM distinct_tab_source;

INSERT INTO distinct_tab_sink2 
    SELECT FirstName, LastName
        FROM distinct_tab_source
         GROUP BY FirstName, LastName; -- Remove duplicate records based on the FirstName and LastName columns.
```

The following figure shows that the results of GROUP BY and DISTINCT functions are the same when the same test data is used. Therefore, the semantics of GROUP BY and DISTINCT are equivalent.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6199758951/p37913.png)

## Use of DISTINCT in the aggregate function COUNT

The use of `DISTINCT` enables the aggregate function `COUNT` to count the number of records after deduplication.

```
COUNT(DISTINCT expression)
```

**Note:** `expression` supports only a single expression.

## Example of the COUNT DISTINCT syntax

-   Test statements

    ```
    CREATE TABLE distinct_tab_source(
        FirstName  VARCHAR,
        LastName VARCHAR
    )WITH(
       type='random'
    ) ;
    
    CREATE TABLE distinct_tab_sink(
        cnt BIGINT  ,
        distinct_cnt BIGINT
    )WITH(
        type = 'print'
    ) ;
    
    INSERT INTO distinct_tab_sink 
        SELECT 
          COUNT(FirstName),  -- Duplicate records are not removed.
          COUNT(DISTINCT FirstName)  -- Duplicate records are removed based on the FirstName column.
        FROM distinct_tab_source;          
    ```

-   Test data

    |FirstName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   Test results

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6199758951/p37914.png)


