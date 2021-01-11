# TopN语句

TopN语句用于对实时数据中某个指标的前N个最值的筛选。Flink SQL可以基于OVER窗口操作灵活地完成TopN的功能。

## 语法

```
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]]
    ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
  FROM table_name)
WHERE rownum <= N [AND conditions]
```

**说明：**

-   `ROW_NUMBER()`：行号计算函数`OVER`的窗口，行号计算从1开始。
-   `PARTITION BY col1[, col2..]` ：指定分区的列，可以不指定。
-   `ORDER BY col1 [asc|desc][, col2 [asc|desc]...]`：指定排序的列和每列的排序方向。

如上语法所示，TopN需要两层Query：

-   查询中使用 `ROW_NUMBER()`窗口函数来对数据根据排序列进行排序并标上排名。
-   外层查询中对排名进行过滤，只取前N条。例如N=10即为取前10条的数据。

在执行过程中，Flink SQL会对输入的数据流根据排序键进行排序。如果某个分区的前N条记录发生了改变，则会将改变的那几条数据以更新流的形式发给下游。

**说明：** 如果需要将TopN的数据输出到外部存储，后接的结果表必须是一个带主键的表。

WHERE条件的限制

为了使Flink SQL能识别出这是一个TopN的query，外层循环中必须要指定 `rownum <= N`的格式来指定前N条记录。此时，您不可以将`rownum`置于某个表达式中，例如`rownum - 5 <= N` 。WHERE条件中，可以额外带上其他条件，但是必须使用`AND`连接条件。

## 示例1

本例中，先统计查询流中每小时、每个城市和关键字被查询的次数，然后输出每小时、每个城市被查询最多的前100个关键字。在输出表中，小时、城市、排名三者可以唯一确定一条记录，所以需要将这三列声明成联合主键（在外部存储中也需要有同样的主键设置）。

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

## 示例2

-   测试数据

    |ip（VARCHAR）|time（VARCHAR）|
    |-----------|-------------|
    |`192.168.1.1`|100000000|
    |`192.168.1.2`|100000000|
    |`192.168.1.2`|100000000|
    |`192.168.1.3`|100030000|
    |`192.168.1.3`|100000000|
    |`192.168.1.3`|100000000|

-   测试语句

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
            SELECT SUBSTRING(`TIME`,1,2) AS start_time,--可以根据真实时间取相应的数值，这里取得是测试数据。
            COUNT(IP) AS cc,
            IP
            FROM  source_table
            GROUP BY SUBSTRING(`TIME`,1,2), IP
        )a
    ) t
    WHERE rownum <= 3 --可以根据真实top值取相应的数值，这里取得是测试数据。
    ```

-   测试结果

    |rownum（BIGINT）|start\_time（VARCHAR）|ip（VARCHAR）|cc（BIGINT）|
    |--------------|--------------------|-----------|----------|
    |1|10|`192.168.1.3`|3|
    |2|10|`192.168.1.2`|2|
    |3|10|`192.168.1.1`|1|


## 无排名优化

-   使用无排名优化，您可以解决数据膨胀问题。
    -   数据膨胀问题

        根据TopN的语法，`rownum`字段会作为结果表的主键字段之一写入结果表。但是这可能导致数据膨胀的问题。例如，收到一条原排名9的更新数据，更新后排名上升到1，则从1到9的数据排名都发生变化了，需要将这些数据作为更新都写入结果表。这样就产生了数据膨胀，导致结果表因为收到了太多的数据而降低更新速度。

    -   无排名优化方法

        结果表中不保存 `rownum`，最终的 `rownum` 由前端计算。因为TopN的数据量通常不会很大，前端排序100个数据很快。当收到一条原排名9、更新后排名上升到1的数据，也只需要发送这一条数据，而不用把排名1到9的数据全发送下去。这种优化能够提升结果表的更新速度。

-   无排名优化语法

    ```
    SELECT col1, col2, col3
    FROM (
     SELECT col1, col2, col3
       ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]]
       ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
     FROM table_name)
    WHERE rownum <= N [AND conditions]
    ```

    语法与上文类似，只是在外层查询中将rownum字段裁剪掉即可。

    **说明：** 在无rownum的场景中，对于结果表主键的定义需要特别小心。如果定义有误，会直接导致TopN结果的不正确。 无rownum场景中，主键应为TopN上游GROUP BY节点的KEY列表。

-   无排名优化示例

    本示例来源于自视频行业的案例。用户每个视频在分发时会产生大量流量，依据视频产生的流量可以分析出最热门的视频。本例用于统计出每分钟流量最大的Top5的视频。

    -   测试语句

        ```
        --从SLS读取数据原始存储表。
        CREATE TABLE sls_cdnlog_stream (
        vid VARCHAR, -- video id
        rowtime TIMESTAMP, -- 观看视频的时间。
        response_size BIGINT, -- 观看产生的流量。
        WATERMARK FOR rowtime as withOffset(rowtime, 0)
        ) WITH (
        type='sls',
        ...
        );
        
        --1分钟窗口统计vid带宽数。
        CREATE VIEW cdnvid_group_view AS
        SELECT vid,
        TUMBLE_START(rowtime, INTERVAL '1' MINUTE) AS start_time,
        SUM(response_size) AS rss
        FROM sls_cdnlog_stream
        GROUP BY vid, TUMBLE(rowtime, INTERVAL '1' MINUTE);
        
        --存储表。
        CREATE TABLE hbase_out_cdnvidtoplog (
        vid VARCHAR,
        rss BIGINT,
        start_time VARCHAR,
           -- 注意结果表中不存储rownum字段。
           -- 特别注意该主键的定义，为TopN上游GROUP BY的KEY。
        PRIMARY KEY(start_time, vid)
        ) WITH (
        type='RDS',
        ...
        );
        
        -- 统计每分钟Top5消耗流量的vid，并输出。
        INSERT INTO hbase_out_cdnvidtoplog
        
        -- 注意次外层查询，不选出rownum字段。
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

    -   测试数据

        |vid（VARCHAR）|rowtime（Timestamp）|response\_size（BIGINT）|
        |------------|------------------|----------------------|
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

    -   测试结果

        |start\_time（VARCHAR）|vid（VARCHAR）|rss（BIGINT）|
        |--------------------|------------|-----------|
        |`2017-12-18 15:00:00`|10000|9000|
        |`2017-12-18 15:00:00`|10007|8000|
        |`2017-12-18 15:00:00`|10006|6000|
        |`2017-12-18 15:00:00`|10005|5000|
        |`2017-12-18 15:00:00`|10002|4000|


