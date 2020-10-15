# DISTINCT

DISTINCT用于SELECT语句中，可以对查询结果进行去重。

## DISTINCT语法

```
SELECT DISTINCT expressions 
FROM tables
...
```

-   `DISTINCT`必须放到开始位置。和其他函数一起使用时，`DISTINCT`也必须放到开始位置，例如，`concat_agg(DISTINCT ',' ,device_id)`。
-   `expressions`是一个或多个expression，可以是具体的column，也可以是function等任何合法表达式。

## DISTINCT示例

-   SQL语句

    Flink SQL中DISTINCT的示例如下所示。

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
    SELECT DISTINCT FirstName, LastName --按照FirstName和LastName两个列进行去重。
    FROM distinct_tab_source;
    ```

-   测试数据

    |FirstName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   查询结果

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6684359951/p37912.png)

    **说明：**

    -   测试数据有4条记录。`DISTINCT FirstName, LastName`将SUN和SHENGRAN去重后，输出3条结果记录。
    -   `SUNS,HENGRAN`和`SUN,SHENGRAN`两条记录并没有被去重，说明`DISTINCT FirstName, LastName`是对两个字段分别处理的，而不是拼接到一起再进行去重。

## DISTINCT的等效写法

在SQL中利用`GROUP BY`语句也可以到达和`DISTINCT`类似的去重效果。`GROUP BY`语法如下。

```
SELECT expressions 
FROM tables
GROUP BY expressions 
;        
```

通过多路输出的方式编写的和DISTINCT示例等效的SQL，示例如下。

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
    SELECT DISTINCT FirstName, LastName --按照FirstName和LastName两个列进行去重。
        FROM distinct_tab_source;

INSERT INTO distinct_tab_sink2 
    SELECT FirstName, LastName
        FROM distinct_tab_source
         GROUP BY FirstName, LastName; --按照FirstName和LastName两个列进行去重。
```

和DISTINCT示例使用相同的测试数据，最终的结果数据相同，说明两种方式的语义一致。

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6684359951/p37913.png)

## DISTINCT在聚合函数COUNT中的作用

`DISTINCT`能使聚合函数`COUNT`统计去重后的计数。

```
COUNT(DISTINCT expression)
```

**说明：** `expression`目前只支持一个表达式。

## COUNT DISTINCT语法示例

-   SQL语法

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
          COUNT(FirstName),  --不去重。
          COUNT(DISTINCT FirstName)  --按照FirstName去重。
        FROM distinct_tab_source;          
    ```

-   测试数据

    |FirstName|LastName|
    |---------|--------|
    |SUNS|HENGRAN|
    |SUN|JINCHENG|
    |SUN|SHENGRAN|
    |SUN|SHENGRAN|

-   测试结果

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6684359951/p37914.png)


