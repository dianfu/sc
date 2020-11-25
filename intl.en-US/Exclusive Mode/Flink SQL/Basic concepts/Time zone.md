---
keyword: time zone
---

# Time zone

This topic describes how to configure a time zone for a Realtime Compute for Apache Flink job to adjust the output data of the DATE and TIME types.

## Configure a time zone

-   Configure a time zone

    You can configure a time zone for a Realtime Compute for Apache Flink job, such as `blink.job.timeZone=America/New_York`, to adjust the output data of the DATE and TIME types. The default time zone is UTC+8.

    -   For more information about time zones, see [Supported time zones](#section_srl_rsf_5fb).
    -   For more information about how to configure a time zone, see [Parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Develop a job.md).
-   Configure different time zones for different source or result tables

    You can configure different time zones for different source or result tables. For example, if you want to read or write data of the TIME, DATE, or TIMESTAMP type in a MySQL database that uses the `America/New_York` time zone, but the `Asia/Shanghai` time zone is used in the computation of a job, you can set a time zone for the source or result table separately in the following way:

    ```
    CREATE TABLE mysql_source_my_table (
     -- ... 
    ) WITH ( 
    timeZone='America/New_York'
     -- ... 
    );
    ```


## Examples

In Realtime Compute for Apache Flink, the time zones that are used in time zone-related functions are custom time zones. The custom time zone `Asia/Shanghai` is used in the following examples.

-   Functions that convert values from the STRING type to the TIMESTAMP type: TO\_TIMESTAMP, TIMESTAMP, and UNIX\_TIMESTAMP

    ```
    TO_TIMESTAMP('2020-08-03 10:59:45.957')
    -- The output is `2020-08-03 10:59:45.957`.
    
    TIMESTAMP '2020-08-03 10:59:45.957'
    -- The output is `2020-08-03 10:59:45.957`.
    
    UNIX_TIMESTAMP('2020-08-03 10:59:45.957')
    -- The output is `1596423585`.
    ```

-   Functions that convert values from the TIMESTAMP type to the STRING type: DATE\_FORMAT and FROM\_UNIXTIME

    **Note:** If the input parameter is of the TIMESTAMP type, the output data varies based on your custom time zone.

    ```
    DATE_FORMAT(TO_TIMESTAMP(1596702949000), 'yyyy-MM-dd HH:mm:ss') 
    -- The output is `2020-08-06 16:35:49`.
    
    DATE_FORMAT('2020-08-06 16:35:49', 'yyyy-MM-dd HH:mm:ss', 'yyyy/MM/dd HH:mm:ss') 
    -- The output is `2020/08/06 16:35:49`. 
    
    FROM_UNIXTIME(1596702949000/1000) 
    -- The output is `2020-08-06 16:35:49`.
    ```

-   Time-related functions

    If the input parameters are of the TIMESTAMP type, the output data of the functions such as EXTRACT, FLOOR, CEIL, and DATE\_DIFF varies based on your custom time zone.

    ```
    -- 1521503999000 2018-03-19T23:59:59+0000, 2018-03-20T07:59:59+0800
     EXTRACT(DAY FROM TO_TIMESTAMP(1521503999000)) 
    -- The output is `20`. This value indicates the twentieth day of the month in the UTC+8 time zone.
    ```

-   Functions used to calculate the current time

    Functions used to calculate the current time, including `LOCALTIMESTAMP()`, `CURRENT_TIMESTAMP()`, `NOW()`, and `UNIX_TIMESTAMP()`

    ```
    -- The current time is 2020-08-03 10:59:45 in the Asia/Shanghai time zone. 
    
    LOCALTIMESTAMP
    -- The output is `2020-08-03 10:59:45.957`. 
    
    CURRENT_TIMESTAMP
    -- The output is `2020-08-03 10:59:45.957`. 
    
    NOW()
    -- The output is `1596423585`. 
    
    UNIX_TIMESTAMP()
    -- The output is `1596423585`.
    ```

-   Functions used to calculate data of the DATE and TIME types

    In Flink SQL, data of the DATE and TIME types is expressed and calculated as integers. A DATE value refers to the number of days that have elapsed after 00:00:00 Thursday, 1 January 1970. A TIME value refers to the number of milliseconds that have elapsed after 00:00:00 on the current day of your time zone. If data of the DATE and TIME types is calculated in user-defined functions \(UDFs\), a time zone offset is added to the Java object when data of the INT type is converted to the `java.sql.Date` or `java.sql.Time` type.


## Supported time zones

For more information about the time zones that are supported by Realtime Compute for Apache Flink, see [Time zones](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/130174/cn_zh/1565675309038/TimeZone.txt).

