# Overview

This topic describes how to build a development environment and use user-defined extensions \(UDXs\) in Realtime Compute for Apache Flink.

**Note:**

-   Only Realtime Compute for Apache Flink in exclusive mode supports UDXs.
-   Blink is developed based on open source Flink SQL by Alibaba Cloud Realtime Compute for Apache Flink to improve computing performance. UDXs can only be used in Blink.

## UDX types

Realtime Compute for Apache Flink supports three types of UDXs, as described in the following table.

|UDX type|Description|
|--------|-----------|
|UDF|User-defined scalar function. The relationship between the input and output of UDFs is one-to-one mapping, which indicates that one value is returned each time a UDF reads one row of data.|
|UDAF|User-defined aggregation function. The relationship between the input and output of UDAFs is many-to-one mapping. A UDAF aggregates multiple input records into one output record. A UDAF can be used with the GROUP BY clause of SQL. For more information, see [Aggregate functions](/intl.en-US/Development/SQL/Builtin functions/Aggregate functions.md).|
|UDTF|User-defined table-valued function. When a UDTF is called, it generates multiple columns or rows of data.|

## Example

Realtime Compute for Apache Flink provides an example of a UDX to facilitate your business development. This example demonstrates the implementation of a UDF, UDAF, and UDTF.

**Note:**

-   In this example, a development environment of the required version is configured. You do not need to build another development environment.
-   The example provides Maven projects. You can use IntelliJ IDEA for development. For more information, see [Develop a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Develop a job.md).

-   Realtime Compute for Apache Flink V3.0

    [Blink\_UDX\_3x](https://github.com/RealtimeCompute/blink_udx_3x)

-   Realtime Compute for Apache Flink V2.0

    [Blink\_UDX\_2x](https://github.com/RealtimeCompute/blink_udx_2x)

-   Realtime Compute for Apache Flink V1.0

    [Blink\_UDX\_1x](https://github.com/RealtimeCompute/blink_udx_1x)


## Build a development environment

The development of UDXs depends on the following JAR packages. You can download the packages as needed.

-   Realtime Compute for Apache Flink versions earlier than V3.2.1
    -   [flink-streaming-java\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327398632/flink-streaming-java_2.11-blink-2.2.4.jar)
    -   [flink-table\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327437386/flink-table_2.11-blink-2.2.4.jar)
    -   [flink-core-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543326995841/flink-core-blink-2.2.4.jar)
-   Realtime Compute for Apache Flink V3.2.1 and later

    Add a [POM dependency](https://search.maven.org/search?q=com.alibaba.blink) based on your open source software version. Download and view the [example of a complete dependency](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111995/cn_zh/1553501574644/pom.xml).

    **Note:** If you want to use Snapshot, you can add a [POM dependency](https://oss.sonatype.org/content/repositories/snapshots/com/alibaba/blink/flink-core/) based on your Snapshot version.


## Register and use resources

1.  Log on to the [Realtime Compute for Apache Flink console](https://stream.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane, click the **Resources** tab.
4.  In the upper-right corner of the **Resources** pane, click **Create Resource**.
5.  In the Upload Resource dialog box, configure resource parameters.

    |Parameter|Description|
    |---------|-----------|
    |Location|You can upload JAR packages only from your machine in the Realtime Compute for Apache Flink console.**Note:** The maximum size of a JAR package that can be uploaded from your machine is 300 MB. If the JAR package exceeds 300 MB, you must upload it to the Object Storage Service \(OSS\) bucket bound to your cluster or use APIs to upload it. |
    |Resource|Click **Upload Resource** and select the resource that you want to reference.|
    |Resource Name|Enter a name for the resource.|
    |Resource Description|Enter a resource description.|
    |Resource Type|Select the type of the resource. The type can be JAR, DICTIONARY, or PYTHON.|

6.  In the **Resources** pane, find the new resource, and move the pointer over **More** in the Actions column.
7.  In the drop-down list, select **Reference**.
8.  In the code editor, declare the UDX at the beginning. The following statement is an example:

    ```
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


## Types of parameters and return values

When you define Java UDXs in Realtime Compute for Apache Flink, you can use Java data types in parameters and return values. The following table lists the mappings between Realtime Compute for Apache Flink and Java data types.

|Data type of Realtime Compute for Apache Flink|Java data type|
|----------------------------------------------|--------------|
|TINYINT|java.lang.Byte|
|SMALLINT|java.lang.Short|
|INT|java.lang.Integer|
|BIGINT|java.lang.Long|
|FLOAT|java.lang.Float|
|DOUBLE|java.lang.Double|
|DECIMAL|java.math.BigDecimal|
|BOOLEAN|java.lang.Boolean|
|DATE|java.sql.Date|
|TIME|java.sql.Time|
|TIMESTAMP|java.sql.Timestamp|
|CHAR|java.lang.Character|
|STRING|java.lang.String|
|VARBINARY|java.lang.byte\[\]|
|ARRAY|Not supported|
|MAP|Not supported|

## Obtain parameters of UDXs

UDXs support an optional `open(FunctionContext context)` method. You can use `FunctionContext` to pass custom configuration items.

Assume that you must add the following two parameters to your job:

```
testKey1=lincoln
test.key2=todd
```

Take a UDTF as an example. You can use `context.getJobParameter` in the open method to obtain parameters. The following code shows an example:

```
public void open(FunctionContext context) throws Exception {
      String key1 = context.getJobParameter("testKey1", "empty");
      String key2 = context.getJobParameter("test.key2", "empty");
      System.err.println(String.format("end open: key1:%s, key2:%s", key1, key2));
}
```

**Note:** For more information, see [Job parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).

