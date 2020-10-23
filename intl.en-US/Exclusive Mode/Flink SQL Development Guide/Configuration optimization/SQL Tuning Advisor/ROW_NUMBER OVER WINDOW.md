---
keyword: [Deduplicate Keep LastRow, Deduplicate Keep FirstRow, ROW\_NUMBER OVER WINDOW]
---

# ROW\_NUMBER OVER WINDOW

You can use the ROW\_NUMBER OVER WINDOW function to efficiently deduplicate source data.

## Background information

Deduplication aims to obtain top N records. Realtime Compute for Apache Flink supports two deduplication policies:

-   Deduplicate Keep FirstRow: retains only the first record under a key. The state data contains only the key information, so the node performance is high after you enable deduplication by using ROW\_NUMBER OVER WINDOW.
-   Deduplicate Keep LastRow: retains only the last record under a key. This policy slightly outperforms the LAST\_VALUE function.

## Optimization method

SQL does not have deduplication syntax. Realtime Compute for Apache Flink uses the ROW\_NUMBER OVER WINDOW function to deduplicate data.

```
SELECT *
FROM (
   SELECT *,
    ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
    ORDER BY timeAttributeCol [asc|desc]) AS rownum
   FROM table_name)
WHERE rownum = 1;
```

|Parameter|Description|
|---------|-----------|
|ROW\_NUMBER\(\)|Calculates the row number. It is a window function that contains an OVER clause. The value starts from 1.|
|PARTITION BY col1\[, col2..\]|Optional. Specifies partition columns for storing primary keys of duplicate records.|
|ORDER BY timeAttributeCol \[asc\|desc\]\)|Specifies the column by which you want to sort data. You must specify a time attribute, which can be proctime or rowtime. You must also specify the sort order, which can be asc \(Deduplicate Keep FirstRow\) or desc \(Deduplicate Keep LastRow\).**Note:**

-   If you do not specify the time attribute, proctime is used by default.
-   If you do not specify the sort order, asc is used by default. |
|rownum|The current row number. Only rownum=1 and rownum<=1 are supported.|

When the ROW\_NUMBER OVER WINDOW function is executed, two levels of queries are performed:

1.  The ROW\_NUMBER\(\) function is used to sort data records under a key by the time attribute and mark the records with their rankings.

    **Note:**

    -   If the time attribute is proctime, Realtime Compute for Apache Flink removes duplicate records based on the time when the records are processed by Realtime Compute for Apache Flink. In this case, the rankings may vary each time.
    -   If the time attribute is rowtime, Realtime Compute for Apache Flink removes duplicate records based on the time when the records are written to Realtime Compute for Apache Flink. In this case, the rankings remain unchanged.
2.  The data records are sorted by their rankings and only the first or last one is retained.

## Sample code

-   Deduplicate Keep FirstRow

    In this example, Realtime Compute for Apache Flink removes duplicate data records in table T based on field b, and retains the first data record that is processed by Realtime Compute for Apache Flink.

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1;
    ```

-   Deduplicate Keep LastRow

    In this example, Realtime Compute for Apache Flink removes duplicate data records in table T based on the b and d fields, and retains the last record that is written to Realtime Compute for Apache Flink.

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1;
    ```


