# DEGREES {#concept_zhx_4kq_dgb .concept}

本文为您介绍如何使用实时计算数学函数DEGREES。

## 语法 {#section_dks_qgp_dgb .section}

``` {#codeblock_n1e_hv8_osb}
DOUBLE DEGREES( double x )
```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|x|DOUBLE|

## 功能描述 {#section_hks_qgp_dgb .section}

将弧度x转换为数值。

## 示例 {#section_iks_qgp_dgb .section}

-   测试语句

    ``` {#codeblock_d4q_cks_tjn}
    SELECT DEGREES (PI()) as result
    FROM T1
    ```

-   测试结果

    |result\(DOUBLE\)|
    |----------------|
    |180.0|


