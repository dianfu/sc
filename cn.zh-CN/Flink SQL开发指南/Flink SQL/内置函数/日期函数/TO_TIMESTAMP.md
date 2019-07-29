# TO\_TIMESTAMP {#concept_ibw_kyq_dgb .concept}

本文为您介绍如何使用实时计算日期函数TO\_TIMESTAMP。

## 语法 {#section_dks_qgp_dgb .section}

``` {#codeblock_rdf_nu7_i8w}
TIMESTAMP TO_TIMESTAMP(BIGINT time)
TIMESTAMP TO_TIMESTAMP(VARCHAR date)
TIMESTAMP TO_TIMESTAMP(VARCHAR date, VARCHAR format)
```

## 入参 {#section_eks_qgp_dgb .section}

|参数|数据类型|
|--|----|
|time|BIGINT **说明：** 单位为毫秒。

 |
|date|VARCHAR **说明：** 默认格式为yyyy-MM-dd HH:mm:ss。

 |
|format|VARCHAR|

## 功能描述 {#section_hks_qgp_dgb .section}

将BIGINT类型的日期或者VARCHAR类型的日期转换成TIMESTAMP类型。

## 示例 {#section_iks_qgp_dgb .section}

-   测试数据

    |timestamp1\(BIGINT\)|timestamp2\(VARCHAR\)|timestamp3\(VARCHAR\)|
    |--------------------|---------------------|---------------------|
    |1513135677000|2017-09-15 00:00:00|20170915000000|

-   测试语句

    ``` {#codeblock_lfr_ry5_xoc .language-sql}
    SELECT TO_TIMESTAMP(timestamp1) as var1,
     TO_TIMESTAMP(timestamp2) as var2,
     TO_TIMESTAMP(timestamp3, 'yyyyMMddHHmmss') as var3
    FROM T1
    ```

-   测试结果

    |var1\(TIMESTAMP\)|var2\(TIMESTAMP\)|var3\(TIMESTAMP\)|
    |-----------------|-----------------|-----------------|
    |2017-12-13 03:27:57.0|2017-09-15 00:00:00.0|2017-09-15 00:00:00.0|


