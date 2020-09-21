---
keyword: [DataStream, read data from Log Service, Log Service]
---

# Read data from Log Service

This topic describes how to run a Flink DataStream job to read data from Alibaba Cloud Log Service.

-   Java Development Kit \(JDK\) 8 is installed on your machine.
-   Maven 3.x is installed on your machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A Logstore is created in Log Service, and test data exists in the Logstore.

The Windows operating system is used in the demo.

**Note:** Only Blink 3.x supports this demo.

## Develop a job

1.  Download and decompress the [SLS\_Demo-master.zip](https://github.com/RealtimeCompute/SLS_Demo) package to your machine.

2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the decompressed SLS\_Demo-master folder and click OK.

3.  Double-click the ConsumerSample.java file in the \\SLS\_Demo\\src\\main\\java\\com\\aliyun\\openservices\\log\\flink directory. Then, set the parameters related to Log Service in the ConsumerSample.java file.

    ```
        private static final String SLS_ENDPOINT = "VPC endpoint";// Use the classic network endpoint or a virtual private cloud (VPC) endpoint in the production environment.
    //  private static final String SLS_ENDPOINT = "public endpoint";// Use the public endpoint in the test environment.
        private static final String ACCESS_KEY_ID = "yourAK";
        private static final String ACCESS_KEY_SECRET = "yourAS";
        private static final String SLS_PROJECT = "yourProject";
        private static final String SLS_LOGSTORE = "yourlogstore";
        // 1. Specify the start offset, which indicates the timestamp to start reading data from Log Service. The timestamp is measured in seconds. 2. To read both full and incremental data from Log Service, set the StartInMs parameter to Consts.LOG_BEGIN_CURSOR.
        // 3. To read only incremental data from Log Service, set the StartInMs parameter to Consts.LOG_END_CURSOR.
        private static final String StartInMs = Consts.LOG_END_CURSOR;
    ```

    **Note:** You must comment out <scope\>provided</scope\> when you perform local debugging in your IDE.

4.  Go to the directory where the pom.xml file is saved. Then, run the following command to package the file:

    ```
    mvn clean package
    ```

    Based on the artifactId parameter that you set in the pom.xml file for your project, a JAR package named flink-log-connector-0.1.21-SNAPSHOT.jar appears in the target directory. This indicates that job development is completed.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Before you publish the job, set the Parallelism parameter for the source table on the **Configurations** tab of the **Development** page. The parallelism of the source table cannot be greater than the number of shards of the source table. Otherwise, a JobManager error occurs when the job starts.

The following example shows the job content:

```
-- Required. The full name of the main class.
--blink.main.class=com.aliyun.openservices.log.flink.ConsumerSample

-- The name of the job.
blink.job.name=sls

-- The resource name of the JAR package that contains the full name of the main class. You must specify this parameter when multiple JAR packages exist.
--blink.main.jar=flink-log-connector-0.1.21-snapshot.jar

-- The default state backend configuration. The configuration takes effect when the job code is not explicitly configured.
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured.
blink.checkpoint.interval.ms=180000
```

**Note:** For more information about how to set custom parameters, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test result

On the Container Log tab of the **Job Administration** page, view information in the taskmanager.out file of the sink node. In this example, the type of the sink node is print.

If the information shown in the following figure appears, Realtime Compute for Apache Flink reads the data from Log Service.

![Test result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1333860061/p140078.png)

