---
keyword: [MiniBatch, MicroBatch]
---

# MiniBatch/MicroBatch调优

您可以通过MiniBatch/MicroBatch调优方式提升吞吐。

## 背景信息

MiniBatch和MicroBatch都是微批处理，只是微批的触发机制略有不同。原理都是缓存一定的数据后再触发处理，以减少对State的访问，从而提升吞吐并减少数据的输出量。但是二者有以下差异：

-   MiniBatch：主要依靠在每个Task上注册的Timer线程来触发微批，需要消耗一定的线程调度性能。
-   MicroBatch：为MiniBatch的升级版，主要基于事件消息来触发微批，事件消息会按您指定的时间间隔在源头插入。MicroBatch在元素序列化效率、反压表现、吞吐和延迟性能上都要优于MiniBatch。

微批处理可以显著的提升系统性能，建议您开启微批处理，例如在聚合场景。但以下场景不建议您开启：

-   微批处理通过增加延迟换取高吞吐，如果您有超低延迟的要求，不建议开启。
-   GroupAggregate聚合度很低（Output/Input \> 0.8）时，一个批次里基本聚合不到数据，不建议开启。

## 优化方式

在**开发**页面右侧**作业参数**中设置blink.microBatch.allowLatencyMs或blink.miniBatch.allowLatencyMs参数，二者效果相同，单位为ms。

**说明：** 如果一个作业中同时设置了blink.microBatch.allowLatencyMs和blink.miniBatch.allowLatencyMs参数，建议二者值保持一致。如果设置的值不一致，则代码下方参数生效。

## 代码示例

-   设置microBatch

    ```
    blink.microBatch.allowLatencyMs=5000
    ```

-   设置miniBatch

    ```
    blink.miniBatch.size=20000
    ```

-   同时设置了microBatch和miniBatch

    ```
    # 防止OOM设置每个批次最多缓存数据的条数。
    blink.miniBatch.size=20000
    blink.microBatch.allowLatencyMs=5000
    blink.miniBatch.allowLatencyMs=6000---该配置生效。
    ```


