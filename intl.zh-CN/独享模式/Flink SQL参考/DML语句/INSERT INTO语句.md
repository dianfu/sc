---
keyword: INSERT INTO
---

# INSERT INTO语句

本文为您介绍在实时计算Flink版中如何使用INSERT INTO语句及使用限制。

## 操作约束

|表类型|使用限制|
|---|----|
|源表|只能引用（FROM），不可执行INSERT。|
|维表|只能引用（JOIN），不可执行INSERT。|
|结果表|仅支持INSERT操作。|
|视图|只能引用（FROM）。|

## 语法

```
INSERT INTO tableName
[ (columnName[ , columnName]*) ]
queryStatement;
```

## 示例

```
INSERT INTO LargeOrders
SELECT * FROM Orders WHERE units > 1000;

INSERT INTO Orders(z,v)
SELECT c,d FROM OO;
```

**说明：**

-   单个实时计算Flink版作业支持在一个SQL作业中包含多个DML操作，同样也允许包含多个数据源、数据目标端和维表。例如，一个作业文件中包含两段业务上完全独立的SQL，分别写到不同的数据目标端。
-   实时计算Flink版不支持单独的SELECT查询，必须在CREATE VIEW或INSERT INTO内才能操作。
-   INSERT INTO支持UPDATE更新。例如，向设置了主键字段的RDS结果表中插入一个KEY值。如果这个KEY值存在就更新，如果不存在就插入一条新的KEY值。

