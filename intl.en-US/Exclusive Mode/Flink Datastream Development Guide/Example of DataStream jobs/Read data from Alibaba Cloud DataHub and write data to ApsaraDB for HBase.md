---
keyword: [read data from DataHub, DataStream, write data to ApsaraDB for HBase]
---

# Read data from Alibaba Cloud DataHub and write data to ApsaraDB for HBase

This topic describes how to run a Flink DataStream job to read data from Alibaba Cloud DataHub and write data to ApsaraDB for HBase.

-   Java Development Kit \(JDK\) 8 is installed on your machine.
-   Maven 3.x is installed on your machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A topic is created in DataHub, and test data exists in the topic.

    **Note:** The test data must contain three fields, which have the data types of BOOLEAN, STRING, and STRING in sequence.

-   An ApsaraDB for HBase instance is created. The ApsaraDB for HBase instance resides in the same region and the same virtual private cloud \(VPC\) as your Realtime Compute for Apache Flink cluster in exclusive mode. A table with several column families is created in the ApsaraDB for HBase instance. For more information about how to use HBase Shell to connect to the HBase cluster, see [Use HBase Shell to access ApsaraDB for HBase](https://www.alibabacloud.com/help/doc-detail/52056.htm?spm=a2c63.l28256.b99.52.5a9c5f1f6Mm6bu).

    **Note:**

    -   ApsaraDB for HBase Standard Edition is used in this topic.
    -   You must add the IP address of your Realtime Compute for Apache Flink cluster to the whitelist of ApsaraDB for HBase.

The Windows operating system is used in the demo.

**Note:** Only Blink 3.x supports this demo.

## Develop a job

1.  Download and decompress the [Hbase\_Demo-master.zip](https://github.com/RealtimeCompute/Hbase_Demo) package to your machine.

2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the decompressed Hbase\_Demo-master folder and click OK.

3.  Double-click the HbaesDemo.java file in the \\Hbase\_Demo-master\\src\\main\\java directory. Then, set the parameters related to DataHub and ApsaraDB for HBase in the HbaesDemo.java file.

    ```
    // Set the parameters related to DataHub.
    //private static String endPoint ="public endpoint";// Access DataHub from the Internet. If you enter an internal endpoint, you do not need to enter the public endpoint.
    private static String endPoint = "inner endpoint";// Access DataHub from an internal network.
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;// Set the time that corresponds to the start offset.
    // Set the parameters related to ApsaraDB for HBase.
    private static String zkQuorum = "yourZK";
    private static String tableName = "yourTable";
    private static String columnFamily = "yourcolumnFamily";
    ```

4.  Go to the directory where the pom.xml file is saved. Then, run the following command to package the file:

    ```
    mvn package -Dcheckstyle.skip
    ```

    Based on the artifactId parameter that you set in the pom.xml file for your project, a JAR package named Hbase\_Demo-1.0-SNAPSHOT-shaded.jar appears in the target directory. This indicates that job development is completed.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Before you publish the job, set the Parallelism parameter for the source table on the **Configurations** tab of the **Development** page. The parallelism of the source table cannot be greater than the number of shards of the source table. Otherwise, a JobManager error occurs when the job starts.

The following example shows the job content:

```
-- Required. The full name of the main class.
blink.main.class=Hbase_Demo.HbaseDemo

-- The name of the job.
blink.job.name=datahub_demo

-- The resource name of the JAR package that contains the full name of the main class. You must specify this parameter when multiple JAR packages exist.
--blink.main.jar=Hbase_Demo-1.0-snapshot.jar

-- The default state backend configuration. The configuration takes effect when the job code is not explicitly configured.
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured.
blink.checkpoint.interval.ms=180000
```

**Note:** For more information about how to set custom parameters, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test result

1.  Send test data to DataHub in the Realtime Compute console.

    ```
    CREATE TABLE kafka_src (
       a BOOLEAN
    ) WITH (
       type = 'random'
    );
    
    CREATE TABLE event_logs (
       `a` BOOLEAN,
        b  VARCHAR,
       `c` VARCHAR
    ) WITH (
        type = 'datahub',
        endPoint = '<yourEndpoint>',
        project = '<yourProject>',
        topic = '<yourTopic>',
        accessId='<yourAccessId>',    
        accessKey='<yourAccessKey>'
    );
    
    INSERT INTO event_logs
    SELECT
        a,'rowkey3' as b,'123' as c
    FROM kafka_src;
    ```

2.  Connect to the HBase cluster. For more information about how to connect to the HBase cluster, see [Use HBase Shell to access ApsaraDB for HBase](https://help.aliyun.com/document_detail/52056.html?spm=a2c4g.11174283.6.595.ccba363fBF1uOn).

3.  Run the `scan 'hbase_sink'` command to query the data written to ApsaraDB for HBase.

    If the information shown in the following figure appears, Realtime Compute for Apache Flink writes the DataHub data to ApsaraDB for HBase.

    ![Test result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0033860061/p139880.png)


## FAQ

Q: If an error similar to the following appears when a job is running, a JAR package conflict occurs. What do I do?

```
java.lang.AbstractMethodError:com.alibaba.fastjson.support.jaxrs.FastJsonAutoDiscoverable.configure(Lcom/alibaba/blink/shaded/datahub/javax/ws/rs/core/FeatureContext;)
```

![JAR package conflict](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0033860061/p88532.png)

A: We recommend that you use the relocation feature of maven-shade-plugin.

```
<relocations combine.self="override">
   <relocation>
       <pattern>org.glassfish.jersey</pattern>
       <shadedPattern>com.alibaba.blink.shaded.datahub.org.glassfish.jersey</shadedPattern>
   </relocation>
</relocations>
```

