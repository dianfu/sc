---
keyword: [Count Distinct, 近似, APPROX\_COUNT\_DISTINCT]
---

# APPROX\_COUNT\_DISTINCT优化

您可以通过APPROX\_COUNT\_DISTINCT（近似Count Distinct）优化提升作业性能。

## 背景信息

Count Distinct优化时，Aggregate节点的State需要保存所有Distinct Key信息。当Distinct Key数目过大时，State的读写开销太大，因此Count Distinct优化存在的性能瓶颈。但在很多场景，完全精确的统计并不那么必要。如果您希望牺牲部分精确度来换取性能上的提升，您可以使用新增的内置函数APPROX\_COUNT\_DISTINCT提升作业性能。APPROX\_COUNT\_DISTINCT支持MiniBatch或Local-Global等Aggregate上的优化，但是需要注意以下几点：

-   输入不含有撤回消息。
-   Distinct Key数目需要足够大，例如UV。如果Distinct Key数目不大，APPROX\_COUNT\_DISTINCT性能相对精确计算提升不大。

## 调优方式

SQL中直接使用APPROX\_COUNT\_DISTINCT\(user\)代替COUNT\(DISTINCT user\)。APPROX\_COUNT\_DISTINCT\(user\)语法格式如下。

```
APPROX_COUNT_DISTINCT(col [, accuracy])
```

其中：

-   col：字段名称，任意类型。
-   accuracy：指定准确率，可选，取值范围为\(0.0, 1.0\)，默认值为0.99，取值越高，准确率越高，state开销越大，性能越低。

## 示例

-   测试数据

    |a \(VARCHAR\)|c \(BIGINT\)|
    |-------------|------------|
    |Hi|1|
    |Hi|2|
    |Hi|3|
    |Hi|4|
    |Hi|5|
    |Hi|6|

-   测试代码

    ```
    SELECT 
      a,
      APPROX_COUNT_DISTINCT(b) as b,
      APPROX_COUNT_DISTINCT(b, 0.9) as c
    FROM MyTable
    GROUP BY a;
    ```

-   测试结果

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |Hi|5|5|


