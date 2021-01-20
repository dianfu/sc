# Online debugging

The Realtime Compute for Apache Flink development platform provides an online debugging environment for you to debug your Realtime Compute for Apache Flink jobs. Compared with local debugging, online debugging consumes more compute units but validates business logic more accurately.

Online debugging uses real data storage resources to reduce the output differences between debugging and production. This helps you identify issues in the debugging phase.

## Procedure

1.  Develop a job. For more information, see [Develop a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Develop a job.md).
2.  Modify the `type` parameter in the data definition language \(DDL\) statements of data storage resources.
    -   Source table: `type = 'random'`
    -   Result table: `type = 'print'`
3.  Publish the job. For more information, see [Publish a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Publish a job.md).
4.  Start the job. For more information, see [Start a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Start a job.md).

## Connectors

The Realtime Compute for Apache Flink development platform provides the following two types of connectors for online debugging:

-   Source table `random`: periodically generates random data of a specific type.
-   Result table `print`: generates computing results.

## Parameters in a connector table

-   random table

    |Parameter|Description|
    |---------|-----------|
    |type|Required. The type of the connector. The value can only be random.|
    |interval|Optional. The time interval at which data is generated. Unit: milliseconds. Default value: 500.|

-   print table

    |Parameter|Description|
    |---------|-----------|
    |type|Required. The type of the connector. The value can only be print.|
    |ignoreWrite|Optional. Specifies whether to ignore write operations. Default value: false. Valid values:     -   false: Data is written to the result table, and logs are generated.
    -   true: Data is not written to the result table. The result table is empty, and no logs are generated. |


## Examples

-   Test code

    ```
    CREATE TABLE random_source (
      instr               VARCHAR
    ) WITH (
      type = 'random'
    );
    
    CREATE TABLE print_sink(
      instr VARCHAR,
      substr VARCHAR,
      num INT
    )with(
      type = 'print'
    );
    
    INSERT INTO print_sink
    SELECT
      instr,
      SUBSTRING(instr,0,5) AS substr,
      CHAR_LENGTH(instr) AS num
    FROM random_source
    ```

-   Test results

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1321659951/p41514.png)


## Query online debugging results

**Note:** Before you query the results of online debugging, make sure that you have published and started the job. For more information, see [Publish a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Publish a job.md) and [Start a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Start a job.md).

To query the results of online debugging, follow these steps:

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration** to go to the Administration page.
3.  In the **Job Name** column, click the name of the job to go to the **Job Administration** page.
4.  In the **Vertex Topology** section, click the required result table node.
5.  On the top of the Execution Vertex page, navigate to **Subtask List** \> **View Logs** to go to the View Logs page.
6.  View the logs.
    -   Output of the print result table

        Click **View Logs** in the Actions column of taskmanager.out.

    -   Output of UDX logs

        If you use user defined extensions \(UDXs\), you can view the logs by using the following methods. For more information about how to use UDXs, see [Overview](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md).

        -   system.out or system.err method

            Click **View Logs** in the Actions column of `taskmanager.out` or `taskmanager.err`.

        -   SLF4J Logger method

            Click **View Logs** in the Actions column of `taskmanager.log`.


