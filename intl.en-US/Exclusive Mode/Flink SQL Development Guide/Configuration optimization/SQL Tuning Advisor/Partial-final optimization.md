---
keyword: [partial-final, Count\(Distinct\)]
---

# Partial-final optimization

You can resolve COUNT\(DISTINCT\) hotspot issues by using partial-final optimization.

## Background information

To resolve the COUNT\(DISTINCT\) hotspot issues, you can change the aggregation process to two-layer aggregation by adding a scattered layer at which data is scattered based on the distinct keys. This is referred to as partial-final optimization. Blink 2.2.0 and later versions support partial-final optimization. Partial-final optimization is suitable for the following scenarios:

-   The COUNT\(DISTINCT\) function has been used but the performance requirement of an aggregate node is not met.
-   The aggregate node where the COUNT\(DISTINCT\) function is executed does not have user-defined aggregate functions \(UDAFs\).

**Note:** Partial-final optimization divides the aggregation process into two layers, which causes additional network shuffling. Therefore, resources are wasted if the data amount is small.

## Optimization method

On the **Parameters** tab of the **Development** page, set blink.partialAgg.enabled to true.

After partial-final optimization is enabled, check whether an expand node is included in the topology or whether the aggregation process has two layers.

## Sample code

```
blink.partialAgg.enabled=true
```

