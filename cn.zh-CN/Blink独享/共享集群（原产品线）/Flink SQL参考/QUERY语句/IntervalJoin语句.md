---
keyword: [IntervalJoin, Time-windowed JOIN]
---

# IntervalJoin语句

IntervalJoin语句可以让两个流进行JOIN时，左流和右流中每条记录只关联另外一条流上同一时间段内的数据，且进行完JOIN后，仍然保留输入流上的时间列，让您继续进行基于Event Time的操作。

## 语法格式

```
SELECT column-names
FROM table1  [AS <alias1>]
[INNER | LEFT | RIGHT |FULL ] JOIN table2 
ON table1.column-name1 = table2.key-name1 AND TIMEBOUND_EXPRESSION
```

**说明：**

-   支持INNER JOIN、LEFT JOIN、RIGHT JOIN和FULL JOIN，如果直接使用JOIN，默认为INNER JOIN。
-   暂不支持SEMI JOIN和ANTI JOIN。
-   TIMEBOUND\_EXPRESSION为左右两个流时间属性列上的区间条件表达式，支持以下三种条件表达式：
    -   ltime = rtime
    -   ltime \>= rtime AND ltime < rtime + INTERVAL '10' MINUTE
    -   ltime BETWEEN rtime - INTERVAL '10' SECOND AND rtime + INTERVAL '5' SECOND

## 示例1 \(基于Event Time\)

统计下单后4个小时内的物流信息。

-   测试数据
    -   订单表（orders）

        |id|productName|orderTime|
        |--|-----------|---------|
        |1|iphone|2020-04-01 10:00:00.0|
        |2|mac|2020-04-01 10:02:00.0|
        |3|huawei|2020-04-01 10:03:00.0|
        |4|pad|2020-04-01 10:05:00.0|

    -   物流表（shipments）

        |shipId|orderId|status|shiptime|
        |------|-------|------|--------|
        |0|1|shipped|2020-04-01 11:00:00.0|
        |1|2|delivered|2020-04-01 17:00:00.0|
        |2|3|shipped|2020-04-01 12:00:00.0|
        |3|4|shipped|2020-04-01 11:30:00.0|

-   测试语句

    ```
    CREATE TABLE Orders(
      id BIGINT,
      productName VARCHAR,
      orderTime TIMESTAMP,
      WATERMARK wk FOR ts as withOffset(orderTime, 2000)  --为rowtime定义Watermark。
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    CREATE TABLE Shipments(
      shipId BIGINT,
      orderId BIGINT,
      status VARCHAR,
      shiptime TIMESTAMP,
      WATERMARK wk FOR ts as withOffset(shiptime, 2000)  --为rowtime定义Watermark。
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    --使用RDS作为结果表
    CREATE TABLE rds_output(
      id BIGINT,
      productName VARCHAR,
      status VARCHAR
    ) WITH (
      type='rds',
      url='<yourDatabaseURL>',
      tableName='<yourDatabaseTablename>',
      userName='<yourDatabaseUserName>',
      password='<yourDatabasePassword>'
    );
    
    INSERT INTO rds_output
    SELECT id, productName, status
    FROM Orders AS o
    JOIN Shipments AS s on o.id = s.orderId AND
         o.ordertime BETWEEN s.shiptime - INTERVAL '4' HOUR AND s.shiptime;
    ```

-   测试结果

    |id\(bigint\)|productName\(varchar\)|status\(varchar\)|
    |------------|----------------------|-----------------|
    |1|iphone|shipped|
    |3|huawei|shipped|
    |4|pad|shipped|


## 示例2 \(基于Processing Time\)

-   测试数据
    -   datahub\_stream1

        |k1|v1|
        |--|--|
        |1|val1|
        |2|val2|
        |3|val3|

    -   datahub\_stream2

        |k1|v1|
        |--|--|
        |1|val1|
        |2|val2|
        |3|val3|

-   测试语句

    ```
    CREATE TABLE datahub_stream1 (
      k1 BIGINT,
      v1 VARCHAR,
      d AS PROCTIME()
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    CREATE TABLE datahub_stream2 (
      k2 BIGINT,
      v2 VARCHAR,
      e AS PROCTIME()
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    --使用RDS作为结果表
    CREATE TABLE rds_output(
      k1 BIGINT,
      v1 VARCHAR,
      v2 VARCHAR
    ) WITH (
      type='rds',
      url='<yourDatabaseURL>',
      tableName='<yourDatabaseTablename>',
      userName='<yourDatabaseUserName>',
      password='<yourDatabasePassword>'
    );
    
    INSERT INTO rds_output
    SELECT k1, v1, v2
    FROM datahub_stream1 AS o
    JOIN datahub_stream2 AS s on o.k1 = s.k2 AND
         o.d BETWEEN s.e - INTERVAL '4' MINUTE AND s.e;
    ```


**说明：** 由于结果取决于两个流里每条数据进入系统的时间，具有不确定性，因此该示例暂不提供预期结果。

