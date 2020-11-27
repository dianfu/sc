---
keyword: remove duplicates
---

# Deduplication statements

You can remove duplicates by executing statements such as FIRST\_VALUE, LAST\_VALUE, and DISTINCT. This topic describes how to execute TopN statements to remove duplicates and describes deduplication considerations.

You can remove duplicates by using the following methods:

-   Keep the first row of duplicate rows.
-   Keep the last row of duplicate rows.

**Note:** The time attribute column in the ORDER BY clause must be defined in the source table.

## Syntax

Flink SQL does not support deduplication statements. You can execute the `ROW_NUMBER OVER WINDOW` statement of Flink SQL to remove duplicates. `ROW_NUMBER OVER WINDOW` is similar to a TopN statement and can be considered as a special TopN statement. For more information about TopN statements, see [TopN](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/TopN statement.md).

```
SELECT *
FROM (
   SELECT *,
     ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
     ORDER BY timeAttributeCol [asc|desc]) AS rownum
   FROM table_name)
WHERE rownum = 1;
```

The following list describes the parameters that are used in the preceding deduplication statement:

-   `ROW_NUMBER()`: calculates the number of a row. The row number starts from 1.
-   `PARTITION BY col1[, col2..]`: specifies one or more columns based on which partitioning is implemented. The specified partition key columns are used as the keys to remove duplicates. This parameter is optional.
-   `ORDER BY timeAttributeCol [asc|desc])`: specifies the column that is used for sorting data. This column must be a time attribute column. The time attribute can be processing time or event time. You can sort rows in ascending or descending order based on the time attribute. If you use the ascending order, the first row of duplicate rows is retained. If you use the descending order, the last row of duplicate rows is retained.
-   The value of `rownum` in the outer query must be 1 \(`= 1`\) or greater than or equal to 1 \(`<= 1`\). In the outer query, the logical operator must be `AND` and you cannot use nondeterministic user-defined functions \(UDFs\).

Based on the preceding syntax, two levels of queries are required to remove duplicates:

-   Subquery: calls the `ROW_NUMBER()` parameter that is used to sort data based on the time attribute column.
-   Outer query: keeps the first row of duplicate rows that have the same primary key and removes the other duplicate rows. The following two sorting orders for the time attribute column are available:
    -   Ascending: `deduplicate keep first row`.
    -   Descending: `deduplicate keep last row`.

If the processing time column is used for sorting, Flink SQL removes duplicates based on the system time. In this case, the result may change each time the deduplication statement is executed. If the event time column is used for sorting, Flink SQL removes duplicates based on the business time. In this case, the result remains unchanged each time the deduplication statement is executed.

## Deduplicate Keep First Row

If you select this policy, the system keeps the first row of duplicate rows that have the same primary key and discards the other duplicate rows. Only keys are stored in the state data of the job. This improves the job performance. You can use the following sample code to remove duplicates and keep the first row of duplicate rows:

```
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
  FROM T
)
WHERE rowNum = 1;
```

In this example, the system removes duplicates in Table T based on Field b. The system keeps the first row of duplicate rows based on the system time. In this example, proctime is a field that has the processing time attribute. To remove deduplicates based on the system time, you can also call the `PROCTIME()` function instead of declaring the proctime filed.

**Note:** In a Blink version later than Blink V3.3.1, the deduplicate keep first row policy allows you to open windows by using the event time attribute. This operation does not trigger retraction.

## Deduplicate Keep Last Row

**Note:** This policy does not allow you to open windows by using the event time attribute.

This policy is used to remove duplicates. The policy keeps only the last row of duplicate rows that have the same primary key. The performance of this policy is higher than that of the LAST\_VALUE function. In the following sample code, the deduplicate keep last row policy is used to remove duplicates:

```
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY proctime DESC) as rowNum
  FROM T
)
WHERE rowNum = 1;
```

## FAQ

What do I do if the following error occurs when `ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY now() as time DESC)` is executed?

```
java.lang.RuntimeException: Can not retract a non-existent record:
    38c30001,1b800000008,1c000000013,85000035343a3731,5d304013.
    This should never happen.
```

This error may occur due to the following two causes:

-   Cause: The `now()` function in the code may result in this error. The TopN function does not support nondeterministic sorting fields. The `now()` function is nondeterministic and returns a different value each time the function is invoked. Therefore, the previous value cannot be found during the retraction.

    Solution: Make sure that you use a deterministic time attribute field, such as a field of the event time attribute. You can also use a field of the processing time attribute in a source table as the deterministic time attribute field.

-   Cause: The value of the `blink.state.ttl.ms` or `state.backend.niagara.ttl.ms` parameter is small.

    Solution: If the specified time-to-live \(TTL\) value is small, use the default value or increase the value.


