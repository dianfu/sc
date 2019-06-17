# UDX overview {#concept_69463_zh .concept}

This topic describes how to build a development environment and use user defined extensions \(UDXs\) in Realtime Compute.

**Note:** Currently, Realtime Compute does not support UDXs in shared mode. UDXs are supported only in exclusive mode.

## Overview {#section_pyp_ccm_cgb .section}

Realtime Compute supports the following UDXs:

-   UDF

    A user defined function \(UDF\) maps zero, one, or multiple scalar values of one record to a new scalar value.

-   UDAF

    A user defined aggregation function \(UDAF\) aggregates multiple records into a single value.

-   UDTF

    A user defined table function \(UDTF\) converts multiple records before generating output records. The number of output records does not need to match the number of input records. UDTFs are the only type of UDXs that can return multiple fields.


## Build the development environment {#section_ck2_gcm_cgb .section}

The development of UDXs depends on some JAR packages of Realtime Compute. Alibaba Cloud provides a UDX development demo \(`RealtimeCompute-udxDemo.gz`\) to help you quickly build the development environment. The demo is a Maven project. You can open it in IntelliJ IDEA and develop your UDXs based on this demo.

The demo implements three simple UDXs \(a UDF, a UDAF, and a UDTF\) for your reference.

`[RealtimeCompute-udxDemo.gz](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/69463/cn_zh/1535104736459/RealtimeCompute-udxDemo.gz)`

The demo depends on the following JAR packages. If you need to use a package separately, click the corresponding link to download it.

`[flink-streaming-java\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327398632/flink-streaming-java_2.11-blink-2.2.4.jar)`

`[flink-table\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327437386/flink-table_2.11-blink-2.2.4.jar)`

`[flink-core-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543326995841/flink-core-blink-2.2.4.jar)`

**Note:** After the demo package is downloaded, modify the `pom. xml` file by referring to the following example:

``` {#codeblock_9lb_6a3_sk3 .language-java}
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-core</artifactId>
        <version>blink-2.2.4-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-table_2.11</artifactId>
        <version>blink-2.2.4-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency> 
    <dependency>
         <groupId>org.apache.flink</groupId>
         <artifactId>flink-streaming-java_2.11</artifactId> 
         <version>blink-2.2.4-SNAPSHOT</version>
         <scope>provided</scope>
    </dependency>
				
```

## Register and use a UDX {#section_ks5_gcm_cgb .section}

After a UDX is developed, compress it into a JAR package. On the Development page, click **Upload**.

After the JAR package is uploaded, select a job and declare the UDX in the job as follows:

``` {#codeblock_fjh_ot2_jnr .language-SQL}
CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
```

1.  [Login Realtime Compute Console](https://stream.console.aliyun.com).
2.  Click **Development** at the top menu .
3.  Click **Resources** at the left side navigation bar.
4.  Click **➕Create Resource** on the **Resources** Tab.
5.  Input resource configuration information.

    |Configuration name|Description|
    |------------------|-----------|
    |Upload mode|Only Upload locally is supported for now.|
    |Resource|Click **Upload Resource** icon and select the resource your need to upload.|
    |Resource Name|Input your resource name.|
    |Resource Description|Input your resource description.|
    |Resource Type|Choose your uploading resource type，JAR、DICTIONARY or PYTHON.|

6.  In **Resources** tab, Hover your mouse on **more**.
7.  Select **Reference**.
8.  Add UDX function statement at the top of SQL query in the job edit window. Example as below.

    ``` {#codeblock_ni6_4hw_9vo .language-SQL}
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


