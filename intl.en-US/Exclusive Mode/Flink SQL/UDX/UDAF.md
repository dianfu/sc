# UDAF

This topic describes how to build a development environment, write business logic code, and publish a user-defined aggregation function \(UDAF\) in Realtime Compute for Apache Flink.

**Note:** Only Realtime Compute for Apache Flink in exclusive mode supports user-defined extensions \(UDXs\).

## Description

A UDAF aggregates multiple values into a single value.

## Methods of the UDAF abstract class

**Note:** A UDAF can be implemented in Java or Scala. However, we recommend that you use Java because Scala data types may cause unnecessary performance overhead.

The following code shows the core methods of the AggregateFunction class.

-   createAccumulator and getValue methods

    ```
    /*
    * @param <T> The type of the output returned by a UDAF.
    * @param <ACC> The accumulator type of a UDAF. An accumulator stores the intermediate aggregation results of a UDAF. You can design an accumulator for each UDAF as required.
    */
    public abstract class AggregateFunction<T, ACC> extends UserDefinedFunction {
    /*
    * Initializes the accumulator in AggregateFunction.
    * The system calls the following method before it aggregates data for the first time.
    */
    public ACC createAccumulator();
    /*
    * The system calls the following method after each aggregation is completed.
    */
    public T getValue(ACC accumulator);
    }
    ```

    **Note:**

    -   The createAccumulator and getValue methods can be defined in the AggregateFunction abstract class.
    -   A UDAF must contain at least one accumulate method.
-   accumulate method

    ```
    public void accumulate(ACC accumulator, ...[ user input]...) ;
    ```

    **Note:**

    -   You must implement an accumulate method to describe how to compute input data and update an accumulator to the aggregation result.
    -   The first parameter of the accumulate method must be an accumulator of the ACC type defined in AggregateFunction. During code execution, the runtime code sends the previous value in the accumulator and the specified upstream data to the accumulate method for aggregation. The upstream data can be of any type and contains any number of data records.
-   retract and merge methods

    The createAccumulator, getValue, and accumulate methods can be used together to design a basic UDAF. However, Realtime Compute for Apache Flink also requires the retract and merge methods in some special scenarios.

    In most scenarios, computing is early firing for an infinite stream. To refine the early fired results, you can implement a retract method. The SQL optimizer automatically determines the conditions in which data needs to be retracted and the operations needed to process data marked with retract tags. You must implement a retract method to retract input data.

    ```
    public void retract(ACC accumulator, ...[ user input]...) ;
    ```

    **Note:**

    -   The retract method is the reverse operation of the accumulate method. For example, in a count UDAF, the number of data records in the computing result increases by one each time the accumulate method is called to process a data record, whereas the number of data records in the result decreases by one each time the retract method is called to process a data record.
    -   Similar to the accumulate method, the first parameter of the retract method must be an accumulator of the ACC type defined in AggregateFunction. During code execution, the runtime code sends the previous value in the accumulator and the specified upstream data to the retract method for aggregation. The upstream data can be of any type and contain any number of data records.
    Realtime Compute for Apache Flink requires the merge method in some scenarios. For example, if you use a session window to aggregate data, you must use the merge method. Realtime Compute for Apache Flink can process out-of-order data. Newly arrived data may fill the gap between two separate sessions, which results in the merge of the two sessions. In this case, you must use the merge method to integrate multiple accumulators into one accumulator.

    ```
    public void merge(ACC accumulator, Iterable<ACC> its);
    ```

    **Note:**

    -   The first parameter of the merge method must be an accumulator of the ACC type defined in AggregateFunction. After the merge method is executed, the state data of AggregateFunction is stored in the first accumulator.
    -   The second parameter of the merge method is an iterator of one or more accumulators of the ACC type.

## Build a development environment

For more information about how to build a development environment, see [Build a development environment](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md).

## Write business logic code

The following Java code is an example:

```
import org.apache.flink.table.functions.AggregateFunction;

public class CountUdaf extends AggregateFunction<Long, CountUdaf.CountAccum> {
    // Define the data schema of the accumulator that stores the state data of a count UDAF.
    public static class CountAccum {
        public long total;
    }

    // Initialize the accumulator of the count UDAF.
    public CountAccum createAccumulator() {
        CountAccum acc = new CountAccum();
        acc.total = 0;
        return acc;
    }

    // Call the getValue method to obtain the result of the count UDAF from the accumulator that stores the UDAF state data.
    public Long getValue(CountAccum accumulator) {
        return accumulator.total;
    }

    // Call the accumulate method to update the accumulator that stores the state data of the count UDAF based on the input data.
    public void accumulate(CountAccum accumulator, Object iValue) {
        accumulator.total++;
    }

    public void merge(CountAccum accumulator, Iterable<CountAccum> its) {
         for (CountAccum other : its) {
            accumulator.total += other.total;
         }
    }
}
```

**Note:** The open and close methods are optional for a subclass of AggregateFunction. For more information, see the examples of [UDF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDF.md) or [UDTF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDTF.md).

## Register and use resources

1.  Log on to the [Realtime Compute for Apache Flink console](https://stream.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane, click the **Resources** tab.
4.  In the upper-right corner of the **Resources** pane, click **Create Resource**.
5.  In the **Upload Resource** dialog box, configure resource parameters.

    |Parameter|Description|
    |---------|-----------|
    |Location|You can only upload JAR packages from your machine in the Realtime Compute for Apache Flink console.**Note:** The maximum size of a JAR package that can be uploaded from your machine is 300 MB. If the JAR package exceeds 300 MB, you must upload it to the Object Storage Service \(OSS\) bucket that is bound to your cluster or use an API to upload it. |
    |Resource|Click **Upload Resource** to select the resource that you want to reference.|
    |Resource Name|Enter a name for the resource.|
    |Resource Description|Enter a resource description.|
    |Resource Type|Select the type of the resource. The type can be JAR, DICTIONARY, or PYTHON.|

6.  In the **Resources** pane, find the new resource, and move the pointer over **More** in the Actions column.
7.  In the drop-down list, select **Reference**.
8.  In the code editor, declare the UDX. The following statement is an example:

    ```
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


## Publish and use a UDAF

For more information about how to publish and use a UDAF, see [Publish a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Publish a job.md) and [Start a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Start a job.md).

## Example

```
-- Use a UDAF to calculate the count.
CREATE FUNCTION countUdaf AS 'com.hjc.test.blink.sql.udx.CountUdaf';

create table sls_stream(
   a int,
   b bigint,
   c varchar
) with (
   type='sls',
   endPoint='yourEndpoint',
   accessKeyId='yourAccessId',
   accessKeySecret='yourAccessSecret',
   startTime='2017-07-04 00:00:00',
   project='<yourPorjectName>',
   logStore='stream-test2',
   consumerGroup='consumerGroupTest3'
);

create table rds_output(
   len1 bigint,
   len2 bigint
) with (
   type='rds',
   url='yourDatabaseURL',
   tableName='<yourDatabaseTableName>',
   userName='<yourDatabaseUserName>',
   password='<yourDatabasePassword>'
);

insert into rds_output
select
   count(a),
   countUdaf(a)
from sls_stream;
```

