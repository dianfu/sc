---
keyword: [Flink SQL optimization, hotspot]
---

# Recommended Flink SQL practices

This topic describes the recommended syntax, configurations, and functions used to optimize Flink SQL performance.

## Optimize the Group By functions

-   Enable microBatch or miniBatch to improve the throughput

    The microBatch and miniBatch policies are both used for micro-batch processing. If either of the policies is enabled, Realtime Compute for Apache Flink processes data when the data cache meets the trigger condition. This reduces the frequency at which Realtime Compute for Apache Flink accesses the state data, and therefore improves the throughput and reduces data output.

    The microBatch and miniBatch policies are different from each other in terms of the trigger mechanism. The miniBatch policy triggers micro-batch processing by using the timer threads that are registered with each task. This consumes some thread scheduling overheads. The microBatch policy is an enhancement of the miniBatch policy. The microBatch policy triggers micro-batch processing based on event messages, which are inserted into the data sources at a specific interval. The microBatch policy outperforms the miniBatch policy because it provides higher data serialization efficiency, reduces backpressure, and achieves higher throughput at a lower latency.

    -   Use scenarios

        Micro-batch processing achieves higher throughput at the expense of higher latency. We recommend that you do not enable micro-batch processing in scenarios that require extremely low latency. However, in data aggregation scenarios, we recommend that you enable micro-batch processing to improve job performance.

        **Note:** You can also enable microBatch to resolve data jitter when data is aggregated in two phases.

    -   Enabling method

        microBatch and miniBatch are disabled by default. To enable them, configure the following parameters:

        ```
        # Enable window miniBatch in Realtime Compute for Apache Flink V3.2 or later. By default, window miniBatch is disabled for Realtime Compute for Apache Flink V3.2 or later.
        sql.exec.mini-batch.window.enabled=true
        # The interval at which a large amount of data is generated. You must specify this parameter when you enable microBatch. We recommend that you set this parameter to the same value as that of blink.miniBatch.allowLatencyMs.
        blink.microBatch.allowLatencyMs=5000
        # When you enable microBatch, you must reserve the settings of the following two miniBatch parameters:
        blink.miniBatch.allowLatencyMs=5000
        # The maximum number of data records that can be cached for each batch. You must set this parameter to avoid the out of memory (OOM) error.
        blink.miniBatch.size=20000
        ```

-   Enable LocalGlobal to resolve common data hotspot issues

    The LocalGlobal policy divides the aggregation process into two phases: local aggregation and global aggregation. They are similar to the combine and reduce phases in MapReduce. In the local aggregation phase, Realtime Compute for Apache Flink locally aggregates a micro batch of data at each input node \(LocalAgg\), and generates an accumulator value for each batch \(accumulator\). In the global aggregation phase, Realtime Compute for Apache Flink merges the accumulator values \(merge\) to obtain the final result \(GlobalAgg\).

    The LocalGlobal policy can eliminate data skew by using local aggregation and resolve data hotspot issues in global aggregation. Therefore, job performance is enhanced. The following figure shows how the LocalGlobal policy resolves data skew issues.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9321659951/p33642.png)

    -   Use scenarios

        You can enable LocalGlobal to improve the performance of general aggregate functions, such as SUM, COUNT, MAX, MIN, and AVG, and resolve data hotspot issues when you execute these functions.

        **Note:** To enable LocalGlobal, you must define a user-defined aggregate function \(UDAF\) to implement the `merge` method.

    -   Enabling method

        In Realtime Compute for Apache Flink V2.0 or later, LocalGlobal is enabled by default. When the blink.localAgg.enabled parameter is set to true, LocalGlobal is enabled. This parameter takes effect only when microBatch or miniBatch is enabled.

    -   Verification

        To determine whether LocalGlobal is enabled, check whether the **GlobalGroupAggregate** or **LocalGroupAggregate** node exists in the generated topology.

-   Enable PartialFinal to resolve data hotspot issues when you execute the COUNT DISTINCT function

    The LocalGlobal policy effectively improves the performance of general aggregate functions, such as SUM, COUNT, MAX, MIN, and AVG. However, it is not effective for improving the performance of the COUNT DISTINCT function. This is because local aggregation cannot effectively remove duplicate distinct keys. As a result, a large amount of data remains stacked up in the global aggregation phase.

    If you execute the COUNT DISTINCT function in Realtime Compute for Apache Flink versions earlier than V2.2.0, you must add a layer that scatters data by a distinct key so that you can divide the aggregation process into two phases to resolve data hotspot issues. Realtime Compute for Apache Flink `V2.2.0` and later versions provide the PartialFinal policy to automatically scatter data and divide the aggregation process. The following figure shows the differences between LocalGlobal and PartialFinal.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0421659951/p33643.png)

    -   Use scenarios

        The PartialFinal policy applies to scenarios in which the aggregation performance cannot meet your requirements when you use the COUNT DISTINCT function.

        **Note:**

        -   You cannot enable PartialFinal in the Flink SQL code that contains UDAFs.
        -   We recommend that you enable PartialFinal only when the amount of data is large. This is because the PartialFinal policy automatically scatters data to two aggregation layers and introduces additional network shuffling. If the amount of data is not large, resources are wasted.
    -   Enabling method

        PartialFinal is disabled by default. To enable PartialFinal, set the `blink.partialAgg.enabled` parameter to true.

    -   Verification

        To determine whether PartialFinal is enabled, check whether **expandable** nodes exist in the generated topology, or whether the number of aggregation layers changes from one to two.

-   Use the AGG WITH FILTER syntax to improve job performance when you use the COUNT DISTINCT function

    **Note:** Only Realtime Compute for Apache Flink V2.2.2 and later versions support this syntax.

    Statistical jobs record unique visitors \(UVs\) in different dimensions, such as UVs of the entire network, UVs of mobile clients, and UVs of PCs. We recommend that you use the standard AGG WITH FILTER syntax instead of AGG WITH CASE WHEN to implement multi-dimensional statistical analysis. The SQL optimizer of Realtime Compute for Apache Flink can analyze the filter parameter. This way, Realtime Compute for Apache Flink can execute the COUNT DISTINCT function on the same field with different filter conditions by sharing the state data. This reduces the read and write operations on the state data. The performance test shows that the use of AGG WITH FILTER improves job performance by one time higher than the use of AGG WITH CASE WHEN.

    -   Use scenarios

        We recommend that you replace the AGG WITH CASE WHEN syntax with the AGG WITH FILTER syntax. This particularly improves job performance when you execute the COUNT DISTINCT function on the same field with different filter conditions.

    -   Original statement

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct case when is_wireless='y' then visitor_id else null end) as UV2
        ```

    -   Optimized statement

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct visitor_id) filter (where is_wireless='y') as UV2
        ```


## Optimize the TopN algorithm

-   TopN algorithm

    If the input streams of TopN are static streams \(such as source\), TopN supports only one algorithm: AppendRank. If the input streams of TopN are dynamic streams \(such as streams that are processed by using the AGG or JOIN function\), TopN supports the following three algorithms in descending order of performance: UpdateFastRank, UnaryUpdateRank, and RetractRank. The name of the algorithm used is contained in the node name in the topology.

    -   UpdateFastRank is the optimal algorithm.

        The following two conditions must be met if you want to use this algorithm:

        -   The input streams must contain the primary key information, such as ORDER BY AVG.
        -   The values of the fields or functions in the ORDER BY clause are updated monotonically in the opposite order of sorting. For example, you can define the ORDER BY clause as ORDER BY COUNT, ORDER BY COUNT\_DISTINCT, or ORDER BY SUM \(positive\) DESC. This optimization is supported only in Realtime Compute for Apache Flink V2.2.2 or later.

            If you want to obtain an optimization plan, you must add a filter condition in which the SUM parameter is positive when you use ORDER BY SUM DESC, and make sure that the value of total\_fee is positive.

            ```
            insert
              into print_test
            SELECT
              cate_id,
              seller_id,
              stat_date,
              pay_ord_amt  -- The rownum field is not included. This reduces the amount of output data to be written to the result table.
            FROM (
                SELECT
                  *,
                  ROW_NUMBER () OVER (
                    PARTITION BY cate_id,
                    stat_date  -- Make sure that the stat_date field is included. Otherwise, the data may be disordered when the state data expires.
                    ORDER
                      BY pay_ord_amt DESC
                  ) as rownum  -- Sort data by the sum of the input data.
                FROM (
                    SELECT
                      cate_id,
                      seller_id,
                      stat_date,
                      -- Note that the values involved in the SUM parameter are positive, so the result of the SUM parameter is monotonically increased. Therefore, the TopN algorithm supports optimization algorithms and it can obtain only the first 100 data records.
                      sum (total_fee) filter (
                        where
                          total_fee >= 0
                      ) as pay_ord_amt
                    FROM
                      random_test
                    WHERE
                      total_fee >= 0
                    GROUP
                      BY cate_name,
                      seller_id,
                      stat_date
                  ) a
                WHERE
                  rownum <= 100
              );                              
            ```

    -   UnaryUpdateRank is second only to UpdateFastRank in terms of performance. To use this algorithm, make sure that the input streams contain the primary key information.
    -   RetractRank ranks last in terms of performance. We recommend that you do not use this algorithm in the production environment. Check input streams. If input streams contain the primary key information, use UnaryUpdateRank or UpdateFastRank to optimize job performance.
-   Optimization method
    -   Exclude the rownum field

        Do not include rownum in the output of TopN. We recommend that you sort the results immediately after they are displayed in the frontend. This can significantly reduce the amount of data that needs to be written to the result table. For more information, see [TopN](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/TopN statement.md).

    -   Increase the cache size of TopN

        TopN provides a state cache to improve the access efficiency of state data. This improves the performance. The following formula is used to calculate the hit rate of TopN cache:

        ```
        cache_hit = cache_size*parallelism/top_n/partition_key_num
        ```

        Take Top100 as an example. Assume that the cache contains 10,000 records and the parallelism is 50. If the number of keys for the PARTITION BY function is 100,000, the cache hit rate equals 5% \(10000 × 50/100/100000 = 5%\). The hit rate is low, which indicates that large amounts of requests will access the disk state data. As a result, job performance significantly deteriorates. Therefore, if the number of keys for the PARTITION BY function is large, you may increase the cache size and heap memory of TopN. For more information, see [Performance optimization by manual configuration](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).

        ```
        ## In this example, if you increase the cache size of TopN from the default value 10000 to 200000, the cache hit rate may reach 100% (200000 × 50/100/100000 = 100%).
        blink.topn.cache.size=200000
        ```

    -   Include a time field in the PARTITION BY function

        For example, you want to include the day field in your statement for a daily ranking. Otherwise, the TopN result may become disordered when the state data expires.


## Optimize the deduplication performance

**Note:** Only Blink 3.2.1 supports efficient deduplication solutions.

Input streams of Realtime Compute for Apache Flink may contain duplicate data. Therefore, deduplication is highly required. Realtime Compute for Apache Flink offers two policies to efficiently remove duplicate data: Deduplicate Keep FirstRow and Deduplicate Keep LastRow.

-   Syntax

    Flink SQL does not support deduplication statements. To reserve the first or last duplicate record under the specified primary key and discard the rest of the duplicate records as required, Realtime Compute for Apache Flink uses the ROW\_NUMBER OVER WINDOW statement of Flink SQL. Deduplication is a special TopN statement.

    ```
    SELECT *
    FROM (
       SELECT *,
        ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
         ORDER BY timeAttributeCol [asc|desc]) AS rownum
       FROM table_name)
    WHERE rownum = 1
    ```

    |Element|Description|
    |-------|-----------|
    |ROW\_NUMBER\(\)|Specifies an over window to compute the row number. The row number starts from 1.|
    |PARTITION BY col1\[, col2..\]|Optional. Specifies the partition key columns that store primary keys of duplicate records.|
    |ORDER BY timeAttributeCol \[asc\|desc\]\)|Specifies the column that sorts records based on a [time attribute](/intl.en-US/Exclusive Mode/Flink SQL/Basic concepts/Time attributes.md) \(proctime or rowtime\). You can sort records in ascending order \(Deduplicate Keep FirstRow\) or descending order \(Deduplicate Keep LastRow\) based on the time attribute.|
    |rownum|Specifies the number of rows. You can set this element in either of the following ways: `rownum = 1` and `rownum <= 1`.|

    Based on the preceding syntax, deduplication includes two steps:

    1.  Use the `ROW_NUMBER()` window function to sort data by the specified time attribute and mark the data with rankings.

        **Note:**

        -   If the time attribute is proctime, Realtime Compute for Apache Flink removes duplicate records based on the time at which the records are processed by Realtime Compute for Apache Flink. In this case, the ranking may vary each time.
        -   If the time attribute is rowtime, Realtime Compute for Apache Flink removes duplicate records based on the time at which the records are written to Realtime Compute for Apache Flink. In this case, the ranking always remains the same.
    2.  Reserve the first record under the specified primary key and remove the rest of the duplicate records.

        **Note:** You can sort records in ascending or descending order of the time attribute.

        -   Deduplicate Keep FirstRow: Realtime Compute for Apache Flink sorts records in ascending order of the time attribute and reserves the first record under the specified primary key.
        -   Deduplicate Keep LastRow: Realtime Compute for Apache Flink sorts records in descending order of the time attribute and reserves the first record under the specified primary key.
-   Deduplicate Keep FirstRow

    If you select the Deduplicate Keep FirstRow policy, Realtime Compute for Apache Flink reserves the first record under the specified primary key but discards the rest of the duplicate records. In this case, the state data stores only the primary key information, and the access efficiency of the state data is significantly improved. The following sample code is an example:

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    **Note:** The preceding code removes duplicate records from table T based on the b field, and reserves the first record under the specified primary key based on the system time. The proctime attribute indicates the processing time attribute. Realtime Compute for Apache Flink sorts data records in table T based on this attribute. To remove duplicate records based on the system time, you can also call the `PROCTIME` function to avoid the need to declare the proctime attribute.

-   Deduplicate Keep LastRow

    If you select the Deduplicate Keep LastRow policy, Realtime Compute for Apache Flink reserves the last record under the specified primary key and discards the rest of the duplicate records. This policy slightly outperforms the LAST\_VALUE function in terms of performance. The following sample code of Deduplicate Keep LastRow is an example:

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    **Note:** The preceding code removes duplicate records in table T based on the b and d fields, and reserves the last record under the specified primary key based on the time at which the records are written to Realtime Compute for Apache Flink. The rowtime attribute indicates the event time at which the records are written to Realtime Compute for Apache Flink. Realtime Compute for Apache Flink sorts records in table T based on this attribute.


## Use efficient built-in functions

-   Use built-in functions to replace user-defined extensions \(UDXs\)

    Built-in functions of Realtime Compute for Apache Flink are under continual optimization. We recommend that you use built-in functions to replace UDXs whenever possible. Realtime Compute for Apache Flink V2.0 optimizes built-in functions in the following aspects:

    -   Improves serialization and deserialization efficiency.
    -   Allows operations at the byte level.
-   Use single-character delimiters in the KEYVALUE function

    The signature of the KEYVALUE function is `KEYVALUE(content, keyValueSplit, keySplit, keyName)`. When keyValueSplit and KeySplit are single-character delimiters, such as a colon \(:\) or a comma \(,\), Realtime Compute for Apache Flink uses an optimization algorithm. Realtime Compute for Apache Flink directly searches for the required keyName values among the binary data without the need to segment the entire content. This improves job performance by approximately 30%.

-   Use the MULTI\_KEYVALUE function when multiple key-value pairs exist

    **Note:** The MULTI\_KEYVALUE function is supported only in Realtime Compute for Apache Flink V2.2.2 or later.

    Job performance is significantly affected if a query involves multiple KEYVALUE functions on the same content. Assume that the content contains 10 key-value pairs. To extract all the 10 values and use them as fields, you must write 10 KEYVALUE functions to parse the content 10 times. As a result, job performance deteriorates.

    In this case, we recommend that you use the table-valued function [MULTI\_KEYVALUE](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/Table-valued functions/MULTI_KEYVALUE.md), which requires only one SPLIT parsing on the content. This improves job performance by 50% to 100%.

-   Use the LIKE operator with caution
    -   To match records that start with the specified content, use `LIKE 'xxx%'`.
    -   To match records that end with the specified content, use `LIKE '%xxx'`.
    -   To match records that contain the specified content, use `LIKE '%xxx%'`.
    -   To match records that are the same as the specified content, use `LIKE 'xxx'`, which is equivalent to `str = 'xxx'`.
    -   To match an underscore \(`_`\), use `LIKE '%seller/id%' ESCAPE '/`. The underscore \(`_`\) is escaped because it is a single-character wildcard in SQL and can match any characters. If you use `LIKE '%seller_id%'`, a lot of results are returned, such as `seller_id`, `seller#id`, `sellerxid`, and `seller1id`. These results may be unsatisfactory.
-   Avoid the use of regular expressions

    Running regular expressions can be time-consuming and may require a hundred more times of computing resources in comparison with other operations such as plus, minus, multiplication, and division. If you run regular expressions under some particular circumstances, your job [may be stuck in an infinite loop](https://stackoverflow.com/questions/4500507/infinite-loop-in-regex-in-java). Therefore, use the LIKE operator whenever possible. For information about common regular expressions, click the following related link:

    -   [REGEXP](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/String functions/REGEXP.md)
    -   [REGEXP\_EXTRACT](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/String functions/REGEXP_EXTRACT.md)
    -   [REGEXP\_REPLACE](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/String functions/REGEXP_REPLACE.md)

## Optimize network transmission

Common partitioner policies include:

-   KeyGroup/Hash: distributes data based on specified keys.
-   Rebalance: distributes data to each channel by using round-robin scheduling.
-   Dynamic-Rebalance: dynamically distributes data to channels with lower load based on the load status of output channels.
-   Forward: similar to Rebalance if keys and channels are unchained. If keys and channels are chained, Realtime Compute for Apache Flink distributes data under specified keys to the related channels.
-   Rescale: distributes data in one-to-many or many-to-one mode between input and output channels.

-   Use Dynamic-Rebalance to replace Rebalance

    When you use Dynamic-Rebalance, Realtime Compute for Apache Flink writes data to subpartitions with lower load based on the amount of buffered data in each subpartition so that it can achieve dynamic load balancing. Compared with the static Rebalance policy, Dynamic-Rebalance can balance the load and improve the overall job performance when the computing capacity of output computing nodes is unbalanced. If you find the load of output nodes is unbalanced when you use Rebalance, you may prefer to use Dynamic-Rebalance. To use Dynamic-Rebalance, set the `task.dynamic.rebalance.enabled` parameter to true. The default value is false.

-   Use Rescale to replace Rebalance

    **Note:** Rescale is supported only in Realtime Compute for Apache Flink V2.2.2 or later.

    Assume that you have 5 parallel input nodes and 10 parallel output nodes. If you use Rebalance, each input node distributes data to all 10 output nodes by using round-robin scheduling. If you use Rescale, each input node only needs to distribute data to two output nodes by using round-robin scheduling. This reduces the number of channels, increases the buffering speed of each subpartition, and therefore improves the network efficiency. When input data is even and the numbers of parallel input and output nodes are the same, you can use Rescale to replace Rebalance. To use Rescale, set the `enable.rescale.shuffling` parameter to true. The default value is false.


## Recommended configuration

In summary, we recommend that you use the following job configuration:

```
# Exactly-once semantics.
blink.checkpoint.mode=EXACTLY_ONCE
# The checkpoint interval in milliseconds.
blink.checkpoint.interval.ms=180000
blink.checkpoint.timeout.ms=600000
# Realtime Compute for Apache Flink V2.X uses Niagara as the state backend and uses it to set the lifecycle (in milliseconds) of the state data.
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000
# Realtime Compute for Apache Flink V2.X enables micro-batch processing with an interval of five seconds.
blink.microBatch.allowLatencyMs=5000
# The allowed latency for a job.
blink.miniBatch.allowLatencyMs=5000
# The size of a batch.
blink.miniBatch.size=20000
# Enable local aggregation. This feature is enabled by default in Realtime Compute for Apache Flink V2.X, but you must manually enable it if you use Realtime Compute for Apache Flink V1.6.4.
blink.localAgg.enabled=true
# Enable PartialFinal to resolve data hotspot issues when you execute the COUNT DISTINCT function in Realtime Compute for Apache Flink V2.X.
blink.partialAgg.enabled=true
# Enable UNION ALL for optimization.
blink.forbid.unionall.as.breakpoint.in.subsection.optimization=true
# Enable OBJECT REUSE for optimization.
#blink.object.reuse=true
# Configure garbage collection for optimization. (You cannot set this parameter if you use a Log Service source table.)
blink.job.option=-yD heartbeat.timeout=180000 -yD env.java.opts='-verbose:gc -XX:NewRatio=3 -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:ParallelGCThreads=4'
# Specify the time zone.
blink.job.timeZone=Asia/Shanghai
```

