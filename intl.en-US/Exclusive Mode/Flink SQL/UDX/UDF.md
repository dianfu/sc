# UDF

This topic describes how to build a development environment, write business logic code, and publish a user-defined scalar function \(UDF\) in Realtime Compute for Apache Flink.

**Note:** Only Realtime Compute for Apache Flink in exclusive mode supports user-defined extensions \(UDXs\).

## Description

A UDF maps zero, one, or multiple scalar values to a new scalar value.

## Build a development environment

For more information about how to build a development environment, see [Build a development environment](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md).

## Write business logic code

To define a UDF, you must extend the ScalarFunction class by implementing the `eval` method. The `open` and `close` methods are optional.

**Note:** UDFs return the same output for the same input by default. However, a UDF where an external service is called may return different output results even if the input values are the same. If a UDF cannot generate the same output for the same input, we recommend that you use the `override isDeterministic` method to make it return `false`. Otherwise, the output may not meet your expectations in some cases. For example, a UDF operator moves forward.

The following sample code is written in Java:

```
package com.hjc.test.blink.sql.udx;

import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.ScalarFunction;

public class StringLengthUdf extends ScalarFunction {
    // The open method is optional.
    // To use the open method, you must add 'import org.apache.flink.table.functions.FunctionContext;' to the code.
    @Override
    public void open(FunctionContext context) {
        }
    public long eval(String a) {
        return a == null ? 0 : a.length();
    }
    public long eval(String b, String c) {
        return eval(b) + eval(c);
    }
    // The close method is optional.
    @Override
    public void close() {
        }
}
```

## Write SQL statements

You can write SQL statements in a specified class. The following example shows the SQL statements in a UDX:

```
-- udf str.length()
CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';

create table sls_stream(
    a int,
    b int,
    c varchar
) with (
    type='sls',
    endPoint='<yourEndpoint>',
    accessKeyId='<yourAccessId>',
    accessKeySecret='<yourAccessSecret>',
    startTime = '2017-07-04 00:00:00',
    project='<yourProjectName>',
    logStore='<yourLogStoreName>',
    consumerGroup='consumerGroupTest1'
);

create table rds_output(
    id int,
    len bigint,
    content VARCHAR
) with (
    type='rds',
    url='yourDatabaseURL',
    tableName='<yourDatabaseTableName>',
    userName='<yourDatabaseUserName>',
    password='<yourDatabasePassword>'
);

insert into rds_output
select
    a,
    stringLengthUdf(c),
    c as content
from sls_stream;
```

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


## Publish and use a UDF

Click **Publish** on the **Development** page for the job where you want to publish a UDF. Then, find the job on the **Administration** page and click **Start** in the Actions column to publish the UDF.

## FAQ

Q: Why does the random number generator always generate the same value at runtime?

A: If no input parameters are passed to a UDX and you do not declare it as nondeterministic, the UDX may be optimized during compilation to return a constant value. To avoid this, you can use the `override isDeterministic` method to make it return `false`.

