# Overview {#concept_62497_zh .concept}

This topic describes data types supported by Realtime Compute and how to convert between different data types.

## Data types supported by Realtime Compute {#section_qv3_vcx_bgb .section}

|Data type|Description|Value range|
|---------|-----------|-----------|
|VARCHAR|Character string with a changeable length|The maximum storage size is 4 MB.|
|BOOLEAN|Logical value|Valid values: TRUE, FALSE, and UNKNOWN.|
|TINYINT|Tiny integer with a length of one byte|Valid values: –128 to 127.|
|SMALLINT|Small integer with a length of two bytes|Valid values: –32768 to 32767.|
|INT|Integer with a length of four bytes|Valid values: –2147483648 to 2147483647.|
|BIGINT|Big integer with a length of eight bytes|Valid values: –9223372036854775808 to 9223372036854775807.|
|FLOAT|Four-byte floating point number|The FLOAT type provides six digits of precision.|
|DECIMAL|Decimal number|Example: The value for `DECIMAL(5, 2)` is `123.45`.|
|DOUBLE|Eight-byte floating point number|The DOUBLE type provides 15 decimal digits of precision.|
|DATE|Date|Example: `DATE'1969-07-20'`.|
|TIME|Time|Example: `TIME '20:17:40'`.|
|TIMESTAMP|Timestamp representing the date and time|Example: `TIMESTAMP '1969-07-20 20:17:40'`.|
|VARBINARY|Binary data|This type corresponds to the `byte[]` type in Java.|

## Data type conversion {#section_hkv_tct_zgb .section}

![Data Types](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40868/156584075534048_en-US.png)

## Examples {#section_mnp_5ct_zgb .section}

-   Test data

    |var1\(VARCHAR\)|big1\(BIGINT\)|
    |---------------|--------------|
    |1000|323|

-   Test statements

    ``` {#codeblock_0nf_0l7_k1m .language-SQL}
    cast (var1 as bigint) as AA;
    cast (big1 as varchar) as BB;
    					
    ```

-   Test results

    |AA \(BIGINT\)|BB \(VARCHAR\)|
    |-------------|--------------|
    |1000|323|


