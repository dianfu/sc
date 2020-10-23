---
keyword: [Partial-Final, Count Distinct]
---

# Partial-Final优化

您可以通过Partial-Final优化的方式解决Count Distinct热点问题。

## 背景信息

通常，为了解决Count Distinct热点问题，您会通过增加按Distinct Key取模的打散层的方式，手动将Aggregate改写为两层聚合。Blink 2.2.0及以后版本支持Count Distinct自动打散，即Partial-Final优化。以下场景适用于开启Partial-Final优化：

-   使用Count Distinct后但无法实现聚合节点性能要求。
-   Count Distinct所在的Aggregate不包含UDAF。

**说明：** 因为partial-final优化会自动将Aggregate打散成两层聚合，引入额外的网络shuffle。所以，在数据量不大的情况下，反而造成资源浪费。

## 调优方式

在**开发**页面右侧**作业参数**中设置blink.partialAgg.enabled=true。

开启Partial-Final优化后，您可以在最终生成的拓扑图的节点名中，观察是否包含Expand节点，或者原来一层的Aggregate变成了两层的Aggregate。

## 示例代码

```
blink.partialAgg.enabled=true
```

