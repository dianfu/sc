---
keyword: [Local-Global, 提升性能, ]
---

# Local-Global优化

您可以通过Local-Global优化解决Aggregate数据倾斜问题。

## 背景信息

Local-Global优化即将原先的Aggregate分成Local和Global两阶段聚合，即MapReduce模型中Combine+Reduce处理模式。第一阶段在上游节点本地攒一批数据进行聚合（localAgg），并输出这次微批的增量值（Accumulator）。第二阶段再将收到的Accumulator merge起来，得到最终的结果（globalAgg）。

Local-Global本质上能够靠localAgg聚合掉倾斜的数据，从而降低globalAgg热点，从而提升性能。Local-Global用于提升SUM、COUNT、MAX、MIN和AVG等普通Aggregate性能，以及解决这些场景下的数据热点问题。

![Local-Global如何解决数据倾斜问题](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3714929951/p161777.png)

## 调优方式

UDAF必须实现merge方法才可以触发Local-Global优化。实现merge方法请参见[示例代码](#section_g07_ksa_z34)。

**说明：** Blink 2.0及以后版本默认开启Local-Global。如果您需要关闭Local-Global，您可以在**作业参数**中，设置blink.localAgg.enabled=false。

## 示例代码

```
import org.apache.flink.table.functions.AggregateFunction;

public class CountUdaf extends AggregateFunction<Long, CountUdaf.CountAccum> {
    //定义存放count udaf的状态的accumulator数据结构
    public static class CountAccum {
        public long total;
    }
    
    //初始化count udaf的accumulator
    public CountAccum createAccumulator() {
        CountAccum acc = new CountAccum();
        acc.total = 0;
        return acc;
    }

    //getValue提供了如何通过存放状态的accumulator计算count UDAF的结果的方法
    public Long getValue(CountAccum accumulator) {
        return accumulator.total;
    }

    //accumulate提供了如何根据输入的数据更新count UDAF存放状态的accumulator
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

