# INSERT INTO语句 {#concept_62509_zh .concept}

本文为您介绍如何在实时使计算中使用INSERT INTO语句以及INSERT INTO的操作约束。

## 语法 {#section_wb3_r14_cgb .section}

``` {#codeblock_hj2_axg_czn .language-sql}
INSERT INTO tableName
      [ (columnName[ , columnName]*) ]
          queryStatement;
```

## 示例 {#section_kdc_db4_cgb .section}

``` {#codeblock_4k7_pt0_iou .language-sql}
INSERT INTO LargeOrders
SELECT * FROM Orders WHERE units > 1000;
INSERT INTO Orders(z, v)
SELECT c,d FROM OO;
```

**说明：** 

-   单个实时计算作业支持在一个SQL作业里面包含多个DML操作，同样也允许包含多个数据源、多个数据目标端、多个维表。例如，在一个作业文件里面包含两段完全业务上独立的SQL，分别写出到不同的数据目标端。
-   实时计算不支持单独的SELECT查询，必须有CREATE VIEW或是在INSERT INTO内才能操作。
-   INSERT INTO支持UPDATE更新。例如，向RDS的表插入一个KEY值。如果这个KEY值存在就会更新，如果不存在就会插入一条新的KEY值。

## 操作约束 {#section_hjn_db4_cgb .section}

|表类型|操作约束|
|---|----|
|源表|只能引用（FROM），不可执行INSERT。|
|维表|只能引用（JOIN），不可执行INSERT。|
|结果表|仅支持INSERT操作。|
|视图|只能引用（FROM）。|

