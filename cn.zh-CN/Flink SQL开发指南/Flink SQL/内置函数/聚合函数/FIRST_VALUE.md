# FIRST\_VALUE {#concept_o2q_hzr_dgb .concept}

本文为您介绍如何使用实时计算聚合函数FIRST\_VALUE。Flink SQL中使用FIRST\_VALUE函数返回数据流的第1条非null数据。

## 语法 {#section_dks_qgp_dgb .section}

``` {#codeblock_wp8_uoi_n0s}
T FIRST_VALUE( T value )
T FIRST_VALUE( T value, BIGINT order )
```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|value|任意参数类型，但输入参数只能为同一种类型。|
|order|BIGINT|

## 功能描述 {#section_hks_qgp_dgb .section}

获取数据流的第1条非null数据。根据order判定FIRST\_VALUE所在的行，取order值最小的记录作为FIRST\_VALUE。

## 示例1 {#section_j6l_7ua_1yq .section}

-   测试数据

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|
    |-----------|--------|------------|
    |1L|1|“Hello”|
    |2L|2|“Hello”|
    |3L|3|“Hello”|
    |4L|4|“Hello”|
    |5L|5|“Hello”|
    |6L|6|“Hello”|
    |7L|7|“Hello World”|
    |8L|8|“Hello World”|
    |20L|20|“Hello World”|

-   测试语句

    ``` {#codeblock_18z_7fq_cxi .language-sql}
    SELECT c,
     FIRST_VALUE(b) 
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(BIGINT\)|
    |------------|--------------|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello World”|7|
    |“Hello World”|7|
    |“Hello World”|7|


## 示例2 {#section_iks_qgp_dgb .section}

-   测试数据

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|order \(BIGINT\)|
    |-----------|--------|------------|----------------|
    |1L|1|“Hello”|7|
    |2L|2|“Hello”|7|
    |3L|3|“Hello”|7|
    |4L|4|“Hello”|7|
    |5L|5|“Hello”|6|
    |6L|6|“Hello”|4|
    |7L|7|“Hello World”|3|
    |8L|8|“Hello World”|2|
    |20L|20|“Hello World”|1|

-   测试语句

    ``` {#codeblock_ymf_wce_jae .language-sql}
    SELECT c,
     FIRST_VALUE(b,order) 
    OVER (
    PARTITION BY c 
    ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING
    ) AS var1
    FROM T1
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|1|
    |“Hello”|5|
    |“Hello”|6|
    |“Hello World”|7|
    |“Hello World”|8|
    |“Hello World”|20|


