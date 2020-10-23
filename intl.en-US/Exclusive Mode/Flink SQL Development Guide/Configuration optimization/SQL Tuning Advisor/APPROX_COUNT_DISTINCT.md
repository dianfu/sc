---
keyword: [COUNT\(DISTINCT\), approximation, APPROX\_COUNT\_DISTINCT]
---

# APPROX\_COUNT\_DISTINCT

You can optimize your job performance by using the APPROX\_COUNT\_DISTINCT function. Compared with COUNT\(DISTINCT\), this function returns an approximate count.

## Background information

When the COUNT\(DISTINCT\) function is used, distinct key information is saved in state data of the aggregate node. If a large number of distinct keys exist, the read/write overhead of state data is large. This causes a bottleneck in job performance optimization. In many cases, accurate computation is not necessary. If you are willing to achieve high job performance at the expense of accuracy, you can use the APPROX\_COUNT\_DISTINCT function. APPROX\_COUNT\_DISTINCT supports miniBatch and local-global optimization on the aggregate node. When you use this function, make sure that the following requirements are met:

-   The input data does not contain retracted messages.
-   A large number of distinct keys, such as unique visits \(UVs\), exist. The APPROX\_COUNT\_DISTINCT function cannot bring obvious benefits if only a small number of distinct keys exist.

## Optimization method

Use APPROX\_COUNT\_DISTINCT\(user\) to replace COUNT\(DISTINCT user\) in the SQL. The syntax of APPROX\_COUNT\_DISTINCT\(user\) is:

```
APPROX_COUNT_DISTINCT(col [, accuracy])
```

where:

-   col indicates the name of a field, which can be of any type.
-   accuracy specifies the calculation accuracy. A larger value indicates higher accuracy, higher state overhead, and lower performance. This field is optional. Valid values: \(0.0, 1.0\). Default value: 0.99.

## Sample code

-   Test data

    |a \(VARCHAR\)|c \(BIGINT\)|
    |-------------|------------|
    |Hi|1|
    |Hi|2|
    |Hi|3|
    |Hi|4|
    |Hi|5|
    |Hi|6|

-   Test statement

    ```
    SELECT 
      a,
      APPROX_COUNT_DISTINCT(b) as b,
      APPROX_COUNT_DISTINCT(b, 0.9) as c
    FROM MyTable
    GROUP BY a;
    ```

-   Test results

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |Hi|5|5|


