# CURRENT\_TIMESTAMP {#concept_ul5_hnq_dgb .concept}

本文为您介绍如何使用实时计算日期函数CURRENT\_TIMESTAMP。

## 语法 {#section_dks_qgp_dgb .section}

``` {#codeblock_55b_h5a_gca}
TIMESTAMP CURRENT_TIMESTAMP
```

## 功能描述 {#section_hks_qgp_dgb .section}

返回当前UTC（GMT+0）时间戳，时间戳单位为毫秒。

## 示例 {#section_iks_qgp_dgb .section}

-   测试语句

    ``` {#codeblock_ajb_6gy_pzd}
    SELECT CURRENT_TIMESTAMP as var1
    FROM T1
    ```

-   测试结果

    |var1\(TIMESTAMP\)|
    |-----------------|
    |2007-04-30 13:10:02.047|


