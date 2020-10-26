---
keyword: product announcement
---

# Announcements

This topic describes the announcements of Realtime Compute for Apache Flink, including version updates, feature updates, and product activities.

## From 21:00 on August 10, 2020 to 02:00 on August 11, 2020: Realtime Compute for Apache Flink is upgraded

Realtime Compute for Apache Flink in exclusive mode in the China \(Hangzhou\) region is upgraded. Services are not interrupted during the upgrade, but you cannot create clusters or scale in or out clusters.

## From 21:00 on April 28, 2020 to 02:00 on April 29, 2020: Realtime Compute for Apache Flink is upgraded

Realtime Compute for Apache Flink in exclusive mode in the China \(Shanghai\) region is upgraded. Services are not interrupted during the upgrade, but you cannot scale in or out clusters.

## From April 20, 2020 to April 22, 2020: The storage service is upgraded

The storage service of Realtime Compute for Apache Flink in shared mode in the China \(Shanghai\) and China \(Shenzhen\) regions is upgraded. This operation makes Realtime Compute for Apache Flink more stable. In normal cases, this upgrade does not affect your services. In special circumstances, jobs of Blink 3.2 and Blink 3.3 fail and then restore to normal during the upgrade.

## August 27, 2019: The Configurations tab is added

The **Configurations** tab is added to the right side of the **Development** page in the Realtime Compute for Apache Flink console. The link to resource configurations is removed from the **Basic Properties** tab.

## May 30, 2019: New features in Realtime Compute in Apache Flink V3.0.0 and later

-   Overview

    The feature of querying vertex information is added. For more information, see [Running information](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data administration/Running information.md).

-   Curve charts

    The curve charts that display the automatic scaling metrics are added. For more information, see [Curve charts](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data administration/Curve charts.md).

-   Timeline

    The Timeline tab is added to the Administration page. For more information, see [Timeline](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data administration/Timeline.md).

-   Properties and parameters

    The feature of querying the details of automatic scaling iterations is added. For more information, see [Properties and Parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data administration/Properties and parameters.md).


## January 24, 2019: Blink 2.2.7 is released

`Blink 2.2.7` is the latest stable version among the `Blink 2.X` versions. This version makes significant improvements to Blink 1.X versions, among which `Blink 1.6.4` is the latest stable version. Blink 2.2.7 uses the new generation of Niagara as the data storage system for the state backend platform. Blink 2.2.7 also optimizes SQL performance and introduces a wide range of new features.

-   Features
    -   SQL
        -   Supports the EMIT statement for windows. The EMIT statement is used to define the output policy. For example, you can view the latest stream processing result every minute within a one-hour window.
        -   Supports miniBatch for joining two streams. Blink 2.2.7 optimizes the retraction mechanism and state data storage structure to improve performance.
        -   Supports data filtering in aggregation. You can aggregate the rows that meet specified filter conditions.
        -   Optimizes local-global aggregation.
        -   Restructures the SQL code during the optimization stage. This shortens the time to compile code.
        -   Allows keys in SortedMapView to support multiple data types, such as BOOLEAN, BYTE, SHORT, INT, LONG, FLOAT, DOUBLE, BIGDECIMAL, BIGINT, BYTE\[\], and STRING.
        -   Optimizes the performance in the scenarios where retractions are involved in the MIN, MAX, FIRST, or LAST function.
        -   Adds multiple scalar functions, including the following functions related to time zones: TO\_TIMESTAMP\_TZ, DATE\_FORMAT\_TZ, and CONVERT\_TZ.
        -   Classifies SQL and connector error messages into different types and offers an error code for each type of error message.
    -   Connector
        -   Allows you to register the connectors of source and result tables by using a custom TableFactory.
        -   Allows you to parse data source types by using user-defined table-valued functions \(UDTFs\).
        -   Allows you to read data from and write data to Kafka.
        -   Allows you to write data to Elasticsearch.
    -   Runtime
        -   Streamlines behaviors such as submitting jobs and obtaining job running results by using Blink sessions.
        -   Supports the plug-in scheduling mechanism. Based on this mechanism, a computing model can customize the scheduling logic to meet your business requirements.
        -   Optimizes the failover-triggered restart strategy. In throttling scenarios, you can restart only the required tasks in a failed job for recovery instead of restarting all the tasks in the job. This optimization improves the efficiency of JobManagers and task failover processing.
    -   State backend
        -   Replaces RocksDBStateBackend with NiagaraStateBackend to improve read/write performance.
        -   `(Experimental) NiagaraStateBackend` separates computing from storage. This state backend helps you restore state data in a few seconds if failovers occur.
-   Items incompatible with `Blink 1.6.4`

    |Item|Impact|Solution|
    |----|------|--------|
    |Table function interface changes|These changes affect the users who use UDTFs.|Update the code to implement the getResultType interface.|
    |Scalar function interface changes|The changes affect the users who use user-defined scalar functions.|Implement the getResultType interface.|
    |Aggregate function interface changes|The changes affect the users who use user-defined aggregation functions \(UDAFs\).|Implement the getAccumulatorType and getResultType interfaces. For example, if the accumulator is of the `Row(STRING, LONG)` type and the Agg result is of the STRING type, use the following code:     ```
public DataType getAccumulatorType\(\) { return
              DataTypes.createRowType\(DataTypes.String, DataTypes.LONG\); } public DataType
              getResultType\(\) { return DataTypes.String; }
    ``` |
    |MapView constructor changes|The parameter that indicates the data type is changed from Types to DataType. This change affects the jobs that declare the MapView in the UDAFs.|Update the code and create a MapView by using the DataType parameter. For example, before the change, the code is `MapView map = new MapView<>(Types.STRING, Types.INT);`. After the change, the code is `MapView map = new MapView<>(DataTypes.STRING, DataTypes.INT);`.|
    |The type of the values returned by the AVG and division functions is changed to DOUBLE if the input parameter is of the LONG or INT type|Before the change, the AVG and division functions return the same type of data as the input parameters. After the change, the data type of the return data is DOUBLE. This may cause type mismatch errors. For example, if the data returned by the AVG or division functions is written to a result table, an error may occur. This is because the type of the data in the result table is different from that of the query fields.|Add the CAST function to perform explicit conversion on the data that is returned by the AVG and division functions.|
    |Precision is considered when data of the BIGDECIMAL type and data of the DECIMAL type are compared|For jobs that use the DECIMAL data type, a type mismatch error may occur. This is because the data type of the actual data is BIGDECIMAL.|Specify the precision when you declare the DECIMAL data type in the job code: `Decimal(38, 18)`.|
    |Semantics used to compare between null and strings|In `Blink 1.X` versions, the SQL statement returns true if a string is null. In `Blink 2.X` versions, the SQL statement returns false to comply with SQL syntax and semantics.|This change affects the scenarios where the comparison between null and strings is involved. For example, if `SPLIT_INDEX(shop_list, ':', 1)` in the `WHERE SPLIT_INDEX(shop_list, ':', 1) <> shop_id` clause returns null, the WHERE clause returns true in `Blink 1.X` versions and false in `Blink 2.X` versions. This facilitates data filtering.|

-   Upgrade to `Blink 2.X`

    To upgrade a `Blink 1.X` version to a `Blink 2.X` version for a job, you must reset a start offset when you restart the job to meet your business requirements.

    1.  Terminate the job. After the job is terminated, the state data is cleared.
    2.  In the lower-right corner of the Development page, select `Blink 2.2.7` from the **Flink Version** drop-down list. Then, publish the job.
    3.  Start the job on the Administration page and specify the start offset.

        If Step 3 fails, perform the following steps for troubleshooting:

        -   Verify and fix the SQL code, and repeat Steps 1 to 3.
        -   If the SQL code cannot be fixed, roll back the Blink version to the original version.
        -   If a JSON plan cannot be generated, specify the following parameters:
            -   `blink.job.option.jmMemMB=4096`
            -   `blink.job.submit.timeoutInSeconds=600`
    For information about the JAR packages for the user-defined extensions \(UDXs\) in `Blink 2.0.1`, see [Overview](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md). The following errors may occur because the version of the UDX JAR package is earlier than the required version or a package conflict occurs.

    ```
    code:[30016], brief info:[get app plan failed], context info:[detail:[java.lang.NoClassDefFoundError: org/apache/flink/table/functions/aggfunctions/DoubleSumWithRetractAggFunction
    at java.lang.ClassLoader.defineClass1(Native Method)
    at java.lang.ClassLoader.defineClass(ClassLoader.java:788)
    at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
    at java.net.URLClassLoader.defineClass(URLClassLoader.java:467)
    at java.net.URLClassLoader.access$100(URLClassLoader.java:73)
    ```


