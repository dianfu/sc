---
keyword: [MiniBatch, MicroBatch]
---

# miniBatch and microBatch

You can increase the throughput by enabling miniBatch or microBatch.

## Background information

Both miniBatch and microBatch are used for micro-batch processing. If you enable miniBatch or microBatch, data processing is triggered when the data in the cache reaches a specified threshold. This reduces the frequency at which Realtime Compute for Apache Flink accesses the state data. This way, the throughput is increased and data output is reduced. However, the two methods have different triggering mechanisms:

-   miniBatch triggers micro-batch processing by using the timer threads that are registered with each task. This requires some thread scheduling overheads.
-   microBatch triggers micro-batch processing by using event messages, which are inserted into the data sources at a specified interval. microBatch is an enhancement of miniBatch. microBatch has higher data serialization efficiency, higher throughput, lower backpressure, and lower latency than miniBatch.

We recommend that you enable micro-batch processing in most scenarios, such as data aggregation, to improve system performance. We recommend that you do not enable micro-batch processing in the following scenarios:

-   Low latency is required. Micro-batch processing achieves high throughput at the expense of latency.
-   The aggregation degree of GroupAggregate is low \(O/I \> 0.8\). In such scenarios, almost no data is aggregated in a batch.

## Optimization method

On the **Parameters** tab of the **Development** page, set blink.microBatch.allowLatencyMs or blink.miniBatch.allowLatencyMs. The two parameters have the same effect. The unit of them is millisecond.

**Note:** If you set both parameters for a job, we recommend that you set them to the same value. If they are set to different values, the second one in the code takes effect.

## Sample code

-   Enable microBatch.

    ```
    blink.microBatch.allowLatencyMs=5000
    ```

-   Enable miniBatch.

    ```
    blink.miniBatch.size=20000
    ```

-   Enable microBatch and miniBatch at the same time.

    ```
    # The maximum number of data records that can be cached in each batch. You must set this parameter to avoid out of memory (OOM).
    blink.miniBatch.size=20000
    blink.microBatch.allowLatencyMs=5000
    blink.miniBatch.allowLatencyMs=6000--- This configuration takes effect.
    ```


