# TopN

A TopN clause is used to compute the largest or smallest N data records of a metric in real-time data. Flink SQL uses an OVER window function to flexibly implement TopN computing.

## Syntax

```
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]]
    ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
  FROM table_name)
WHERE rownum <= N [AND conditions]
```

**Note:**

-   `ROW_NUMBER()`: specifies an `OVER` window function to compute the row number. The value starts from 1.
-   `PARTITION BY col1[, col2..]`: specifies one or more partitioning columns. This parameter is optional.
-   `ORDER BY col1 [asc|desc][, col2 [asc|desc]...]`: specifies the columns based on which you want to sort data and the sorting order of each column.

As shown in the preceding syntax, a TopN clause requires two levels of queries.

-   In the subquery, the `ROW_NUMBER()` window function is used to sort data based on the specified columns and mark the data with rankings.
-   In the outer query, only the first N data records in the ranking list are obtained. For example, if N is set to 10, the first 10 data records are obtained.

During the execution, Flink SQL sorts an input data stream based on the sort key. If the first N data records in a partition are changed, the updated data is sent downstream as an update stream.

**Note:** Therefore, if you want to export the TopN data to external storage, the target result table must contain a primary key.

WHERE

To enable Flink SQL to identify a TopN query, use `rownum <= N` in the outer query to specify the first N records. Do not place `rownum` in an expression, for example, `rownum - 5 <= N`. If you specify multiple conditions in the WHERE clause, you must use `AND` to join the conditions.

## Example 1

You can use the following example to return the top 100 keywords that are queried the most in each city within a specific hour. The hour, city, and ranking columns in the output table identify a unique record. Therefore, you must declare the three columns as a composite key. The key must also be configured in the external storage.

```
CREATE TABLE rds_output (
  rownum BIGINT,
  start_time BIGINT,
  city VARCHAR,
  keyword VARCHAR,
  pv BIGINT,
  PRIMARY KEY (rownum, start_time, city)
) WITH (
  type = 'rds',
  ...
)

INSERT INTO rds_output
SELECT rownum, start_time, city, keyword, pv
FROM (
  SELECT *,
     ROW_NUMBER() OVER (PARTITION BY start_time, city ORDER BY pv desc) AS rownum
  FROM (
        SELECT SUBSTRING(time_str,1,12) AS start_time, 
            keyword,
            count(1) AS pv,
            city
        FROM tmp_search
        GROUP BY SUBSTRING(time_str,1,12), keyword, city
    ) a 
) t
WHERE rownum <= 100
```

## Example 2

-   Test data

    |ip \(VARCHAR\)|time \(VARCHAR\)|
    |--------------|----------------|
    |`192.168.1.1`|100,000,000|
    |`192.168.1.2`|100,000,000|
    |`192.168.1.2`|100,000,000|
    |`192.168.1.3`|100,030,000|
    |`192.168.1.3`|100,000,000|
    |`192.168.1.3`|100,000,000|

-   Test statements

    ```
    CREATE TABLE source_table (
      IP VARCHAR,
      `TIME` VARCHAR 
    )WITH(
      type='datahub',
      endPoint='<yourEndpoint>',
      project='<yourProjectName>',
      topic='<yourTopicName>',
      accessId='<yourAccessId>',
      accessKey='<yourAccessSecret>'
    );
    
    CREATE TABLE result_table (
      rownum BIGINT,
      start_time VARCHAR,
      IP VARCHAR,
      cc BIGINT,
      PRIMARY KEY (start_time, IP)
    ) WITH (
      type = 'rds',
      url='<yourDatabaseAddress>',
      tableName='blink_rds_test',
      userName='<yourDatabaseUserName>',
      password='<yourDatabasePassword>'
    );
    INSERT INTO result_table
    SELECT rownum,start_time,IP,cc
    FROM (
      SELECT *,
         ROW_NUMBER() OVER (PARTITION BY start_time ORDER BY cc DESC) AS rownum
      FROM (
            SELECT SUBSTRING(`TIME`,1,2) AS start_time,-- You can specify a value based on the actual time. The data specified here is an example.
            COUNT(IP) AS cc,
            IP
            FROM  source_table
            GROUP BY SUBSTRING(`TIME`,1,2), IP
        )a
    ) t
    WHERE rownum <= 3 -- You can specify a value based on the number of data records you want to obtain. The data specified here is an example.
    ```

-   Test results

    |rownum \(BIGINT\)|start\_time \(VARCHAR\)|ip \(VARCHAR\)|cc \(BIGINT\)|
    |-----------------|-----------------------|--------------|-------------|
    |1|10|`192.168.1.3`|3|
    |2|10|`192.168.1.2`|2|
    |3|10|`192.168.1.1`|1|


## No ranking number optimization

-   You can use no ranking number optimization to solve the data bloat issue.
    -   Data bloat

        Based on the TopN syntax, the `rownum` field is written into a result table as one of its primary keys. This may lead to data bloat. For example, if the ranking of a data record rises from the ninth to the first after a data update, the rankings of the records from the first to the ninth all change. The changes must be updated in the result table. This results in data bloat. The data update in the result table may slow down because an excessive volume of data is received.

    -   Method of no ranking number optimization

        To avoid data bloat, remove `rownum` from the result table and compute `rownum` at the front end. The volume of TopN data records is not large, so the top 100 data records can be obtained quickly at the front end. In this case, if the ranking of a data record rises from the ninth to the first after an update, only this record needs to be delivered to the result table. This accelerates data update in the result table.

-   Syntax

    ```
    SELECT col1, col2, col3
    FROM (
     SELECT col1, col2, col3
       ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]]
       ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
     FROM table_name)
    WHERE rownum <= N [AND conditions]
    ```

    The syntax is similar to the original TopN syntax. You only need to remove the rownum field from the outer query.

    **Note:** If the rownum field is removed, pay attention to the definition of the primary keys in the result table. If the definition is incorrect, the TopN query result is incorrect. The primary keys must be defined in the key list at the GROUP BY node before the TopN clause.

-   Example

    This example is a case from a customer in the video industry. Heavy traffic is generated each time a video is distributed. You can identify the most popular videos based on the video traffic. The following example identifies the top 5 videos that generate the most traffic per minute.

    -   Test statements

        ```
        -- Read the original data storage table from Log Service.
        CREATE TABLE sls_cdnlog_stream (
        vid VARCHAR, -- The video ID.
        rowtime TIMESTAMP, -- The time when the video was watched.
        response_size BIGINT, -- The traffic generated for watching the video.
        WATERMARK FOR rowtime as withOffset(rowtime, 0)
        ) WITH (
        type='sls',
        ...
        );
        
        -- Compute the consumed bandwidth by video ID in a 1-minute window.
        CREATE VIEW cdnvid_group_view AS
        SELECT vid,
        TUMBLE_START(rowtime, INTERVAL '1' MINUTE) AS start_time,
        SUM(response_size) AS rss
        FROM sls_cdnlog_stream
        GROUP BY vid, TUMBLE(rowtime, INTERVAL '1' MINUTE);
        
        -- Create a result table.
        CREATE TABLE hbase_out_cdnvidtoplog (
        vid VARCHAR,
        rss BIGINT,
        start_time VARCHAR,
           -- Do not store the rownum field in the result table.
           -- Pay attention to the definition of the primary keys. The primary keys must be defined in the key list at the GROUP BY node before the TopN clause.
        PRIMARY KEY(start_time, vid)
        ) WITH (
        type='RDS',
        ...
        );
        
        -- Identify and export the IDs of the top 5 videos that generate the most traffic per minute.
        INSERT INTO hbase_out_cdnvidtoplog
        
        -- Do not include the rownum field in the outer query.
        SELECT vid, rss, start_time FROM
        (
        SELECT
        vid, start_time, rss,
        ROW_NUMBER() OVER (PARTITION BY start_time ORDER BY rss DESC) as rownum
        FROM
        cdnvid_group_view
        )
        WHERE rownum <= 5;
        ```

    -   Test data

        |vid \(VARCHAR\)|rowtime \(TIMESTAMP\)|response\_size \(BIGINT\)|
        |---------------|---------------------|-------------------------|
        |10000|`2017-12-18 15:00:10`|2000|
        |10000|`2017-12-18 15:00:15`|4000|
        |10000|`2017-12-18 15:00:20`|3000|
        |10001|`2017-12-18 15:00:20`|3000|
        |10002|`2017-12-18 15:00:20`|4000|
        |10003|`2017-12-18 15:00:20`|1000|
        |10004|`2017-12-18 15:00:30`|1000|
        |10005|`2017-12-18 15:00:30`|5000|
        |10006|`2017-12-18 15:00:40`|6000|
        |10007|`2017-12-18 15:00:50`|8000|

    -   Test results

        |start\_time \(VARCHAR\)|vid \(VARCHAR\)|rss \(BIGINT\)|
        |-----------------------|---------------|--------------|
        |`2017-12-18 15:00:00`|10000|9000|
        |`2017-12-18 15:00:00`|10007|8000|
        |`2017-12-18 15:00:00`|10006|6000|
        |`2017-12-18 15:00:00`|10005|5000|
        |`2017-12-18 15:00:00`|10002|4000|


