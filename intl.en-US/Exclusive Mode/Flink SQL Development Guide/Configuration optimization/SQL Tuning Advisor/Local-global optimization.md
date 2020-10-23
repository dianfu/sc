---
keyword: [local-global, performance improvement, ]
---

# Local-global optimization

You can use local-global optimization to resolve data skew issues of an aggregate node.

## Background information

When you use local-global optimization, the aggregation process is divided into two phases: local aggregation and global aggregation. They are similar to the combine and reduce phases in MapReduce. In the local aggregation phase, the system aggregates data that is locally buffered at the input node into batches and generates an accumulator for each batch of data. In the global aggregation phase, the system merges the accumulators to obtain the global aggregation result.

Local-global optimization can eliminate data skews by using local aggregation and resolve the data hotspot issues in global aggregation. This improves job performance. You can use local-global optimization to improve the performance of common aggregate functions, such as SUM, COUNT, MAX, MIN, and AVG. You can also use it to resolve data hotspot issues that occur when you use these functions.

![Use local-global optimization to resolve data skew issues](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0993443061/p161777.png)

## Optimization method

A user-defined aggregate function \(UDAF\) must execute the merge method to trigger local-global optimization. For more information about how to execute the merge method, see [Sample code](#section_g07_ksa_z34).

**Note:** In Blink 2.0 and later versions, local-global optimization is enabled by default. If you want to disable local-global optimization, set blink.localAgg.enabled to false in **job parameters**.

## Sample code

```
import org.apache.flink.table.functions.AggregateFunction;

public class CountUdaf extends AggregateFunction<Long, CountUdaf.CountAccum> {
    // Define the data structure of the accumulator that stores state data of the COUNT UDAF.
    public static class CountAccum {
        public long total;
    }
    
    // Initialize the accumulator of the COUNT UDAF.
    public CountAccum createAccumulator() {
        CountAccum acc = new CountAccum();
        acc.total = 0;
        return acc;
    }

    // The getValue method is used to compute the result of the COUNT UDAF based on the accumulator that stores state data.
    public Long getValue(CountAccum accumulator) {
        return accumulator.total;
    }

    // The accumulate method is used to update the accumulator that is used by the COUNT UDAF to store state data based on input data.
    public void accumulate(CountAccum accumulator, Object iValue) {
        accumulator.total++;
    }
    
    public void merge(CountAccum accumulator, Iterable<CountAccum> its) {
         for (CountAccum other : its) {
            accumulator.total += other.total;
         }
    }
}
```

