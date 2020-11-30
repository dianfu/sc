# 维表JOIN语句

对于每条流式数据，可以关联一个外部维表数据源，为实时计算Flink版提供数据关联查询。

**说明：** 维表是一张不断变化的表，在维表JOIN时，需指明该条记录关联维表快照的时刻。维表JOIN仅支持对当前时刻维表快照的关联，未来会支持关联左表rowtime所对应的维表快照。关于维表的详细介绍，请参见[概述](/intl.zh-CN/独享模式/Flink SQL参考/DDL语句/创建数据维表/概述.md)。

## 维表JOIN语法

```
SELECT column-names
FROM table1  [AS <alias1>]
[LEFT] JOIN table2 FOR SYSTEM_TIME AS OF PROCTIME() [AS <alias2>]
ON table1.column-name1 = table2.key-name1;
```

事件流JOIN白名单维表，示例如下。

```
SELECT e.*, w.*
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id;
```

**说明：**

-   维表支持`INNER JOIN`和`LEFT JOIN`，不支持`RIGHT JOIN`或`FULL JOIN`。
-   必须加上`FOR SYSTEM_TIME AS OF PROCTIME()`，表示JOIN维表当前时刻所看到的每条数据。
-   源表后面进来的数据只会关联当时维表的最新信息，即JOIN行为只发生在处理时间（Processing Time）。如果JOIN行为发生后，维表中的数据发生了变化（新增、更新或删除），则已关联的维表数据不会被同步变化。
-   ON条件中必须包含维表所有的PRIMARY KEY的等值条件（且要求与真实表定义一致）。此外，ON条件中也可以有其他等值条件。
-   如果您有一对多JOIN需求，请在维表DDL INDEX中指定关联的KEY，详情请参见[INDEX语法](/intl.zh-CN/独享模式/Flink SQL参考/DDL语句/创建数据维表/概述.md)。
-   维表和维表不能进行JOIN。
-   ON条件中维表字段不能使用CAST等类型转换函数。如果您有类型转换需求，请在源表字段进行操作。

## 示例

-   测试数据

    |id（bigint）|name（varchar）|age（bigint）|
    |----------|-------------|-----------|
    |1|lilei|22|
    |2|hanmeimei|20|
    |3|libai|28|

    |name（varchar）|phoneNumber（bigint）|
    |-------------|-------------------|
    |dufu|18867889855|
    |baijuyi|18867889856|
    |libai|18867889857|
    |lilei|18867889858|

-   测试语句

    ```
    CREATE TABLE datahub_input1 (
    id            BIGINT,
    name        VARCHAR,
    age           BIGINT
    ) WITH (
    type='datahub'
    );
    
    create table phoneNumber(
    name VARCHAR,
    phoneNumber bigint,
    primary key(name),
    PERIOD FOR SYSTEM_TIME
    )with(
    type='rds'
    );
    
    CREATE table result_infor(
    id bigint,
    phoneNumber bigint,
    name VARCHAR
    )with(
    type='rds'
    );
    
    INSERT INTO result_infor
    SELECT
    t.id,
    w.phoneNumber,
    t.name
    FROM datahub_input1 as t
    JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w
    ON t.name = w.name;
    ```

-   测试结果

    |id（bigint）|phoneNumber（bigint）|name（varchar）|
    |----------|-------------------|-------------|
    |1|18867889858|lilei|
    |3|18867889857|libai|


