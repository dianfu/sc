# OVER窗口

OVER窗口（OVER Window）是传统数据库的标准开窗，不同于Group By Window，OVER窗口中每1个元素都对应1个窗口。OVER窗口可以按照实际元素的行或实际的元素值（时间戳值）确定窗口，因此流数据元素可能分布在多个窗口中。

在应用OVER窗口的流式数据中，每1个元素都对应1个OVER窗口。每1个元素都触发1次数据计算，每个触发计算的元素所确定的行，都是该元素所在窗口的最后1行。在实时计算的底层实现中，OVER窗口的数据进行全局统一管理（数据只存储1份），逻辑上为每1个元素维护1个OVER窗口，为每1个元素进行窗口计算，完成计算后会清除过期的数据。

## 语法

```
SELECT
    agg1(col1) OVER (definition1) AS colName,
    ...
    aggN(colN) OVER (definition1) AS colNameN
FROM Tab1;
```

-   agg1\(col1\)：按照GROUP BY指定col1列对输入数据进行聚合计算。
-   OVER \(definition1\)：OVER窗口定义。
-   AS colName：别名。

**说明：**

-   agg1到aggN所对应的OVER definition1必须相同。
-   外层SQL可以通过AS的别名查询数据。

## 类型

Flink SQL中对OVER窗口的定义遵循标准SQL的定义语法，传统OVER窗口没有对其进行更细粒度的窗口类型命名划分。按照计算行的定义方式，OVER Window可以分为以下两类：

-   ROWS OVER Window：每1行元素都被视为新的计算行，即每1行都是一个新的窗口。
-   RANGE OVER Window：具有相同时间值的所有元素行视为同一计算行，即具有相同时间值的所有行都是同一个窗口。

## 属性

|正交属性|说明|proctime|eventtime|
|----|--|--------|---------|
|ROWS OVER Window|按照实际元素的行确定窗口。|支持|支持|
|RANGE OVER Window|按照实际的元素值（时间戳值）确定窗口。|支持|支持|

## Rows OVER Window语义

-   窗口数据

    ROWS OVER Window的每个元素都确定一个窗口。ROWS OVER Window分为Unbounded（无界流）和Bounded（有界流）两种情况。

    Unbounded ROWS OVER Window数据示例如下图所示。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0384359951/p34339.png)

    **说明：** 虽然上图所示窗口user1的w7、w8及user2的窗口w3、w4都是同一时刻到达，但它们仍然在不同的窗口，这一点与RANGE OVER Window不同。

    Bounded ROWS OVER Window数据以3个元素（往前2个元素）的窗口为例，如下图所示。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0384359951/p34340.png)

    **说明：** 虽然上图所示窗口user1的w5、w6及user2的窗口w1、w2都是同一时刻到达，但它们仍然在不同的窗口，这一点与RANGE OVER Window不同。

-   窗口语法

    ```
    SELECT
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)]
         ORDER BY timeCol
         ROWS 
         BETWEEN (UNBOUNDED | rowCount) PRECEDING AND CURRENT ROW) AS colName, ...
    FROM Tab1;       
    ```

    -   value\_expression：分区值表达式。
    -   timeCol：元素排序的时间字段。
    -   rowCount：定义根据当前行开始向前追溯几行元素。
-   案例

    以Bounded ROWS OVER Window场景为例。假设，一张商品上架表，包含有商品ID、商品类型、商品上架时间、商品价格数据。要求输出在当前商品上架之前同类的3个商品中的最高价格。

    -   测试数据

        |商品ID|商品类型|上架时间|销售价格|
        |----|----|----|----|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|

    -   测试代码

        ```
        CREATE TABLE tmall_item(
           itemID VARCHAR,
           itemType VARCHAR,
           onSellTime TIMESTAMP,
           price DOUBLE,
           WATERMARK onSellTime FOR onSellTime as withOffset(onSellTime, 0)
        ) 
        WITH (
          type = 'sls',
           ...
        );
        
        SELECT
            itemID,
            itemType,
            onSellTime,
            price,  
            MAX(price) OVER (
                PARTITION BY itemType 
                ORDER BY onSellTime 
                ROWS BETWEEN 2 preceding AND CURRENT ROW) AS maxPrice
          FROM tmall_item;
        ```

    -   测试结果

        |itemID|itemType|onSellTime|price|maxPrice|
        |------|--------|----------|-----|--------|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|50|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|60|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|60|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|20|


## RANGE OVER Window语义

-   窗口数据

    RANGE OVER Window所有具有共同元素值（元素时间戳）的元素行确定一个窗口，RANGE OVER Window分为Unbounded和Bounded的两种情况。

    Unbounded RANGE OVER Window数据示例如下图所示。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0384359951/p34341.png)

    **说明：** 上图所示窗口user1的w7、user2的窗口w3，两个元素同一时刻到达，属于相同的window，这一点与ROWS OVER Window不同。

    Bounded RANGE OVER Window数据，以3秒中数据`(INTERVAL '2' SECOND)`的窗口为例，如下图所示。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0384359951/p34342.png)

    **说明：** 上图所示窗口user1的w6、user2的窗口w3，元素都是同一时刻到达，属于相同的window，这一点与ROWS OVER Window不同。

-   窗口语法

    ```
    SELECT
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)]
         ORDER BY timeCol
         RANGE 
         BETWEEN (UNBOUNDED | timeInterval) PRECEDING AND CURRENT ROW) AS colName,
    ...
    FROM Tab1;
    ```

    -   value\_expression：进行分区的字表达式。
    -   timeCol：元素排序的时间字段。
    -   timeInterval：定义根据当前行开始向前追溯指定时间的元素行。
-   案例

    Bounded RANGE OVER Window场景示例：假设一张商品上架表，包含有商品ID、商品类型、商品上架时间、商品价格数据。需要求比当前商品上架时间早2分钟的同类商品中的最高价格。

    -   测试数据

        |商品ID|商品类型|上架时间|销售价格|
        |----|----|----|----|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|

    -   测试代码

        ```
        CREATE TABLE tmall_item(
           itemID VARCHAR,
           itemType VARCHAR,
           onSellTime TIMESTAMP,
           price DOUBLE,
           WATERMARK onSellTime FOR onSellTime as withOffset(onSellTime, 0)
        ) 
        WITH (
          type = 'sls',
           ...
        );
        
        SELECT  
            itemID,
            itemType, 
            onSellTime, 
            price,  
            MAX(price) OVER (
                PARTITION BY itemType 
                ORDER BY onSellTime 
                RANGE BETWEEN INTERVAL '2' MINUTE preceding AND CURRENT ROW) AS maxPrice
          FROM tmall_item;          
        ```

    -   测试结果

        |itemID|itemType|onSellTime|price|maxPrice|
        |------|--------|----------|-----|--------|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|50|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|60|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|40|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|20|


