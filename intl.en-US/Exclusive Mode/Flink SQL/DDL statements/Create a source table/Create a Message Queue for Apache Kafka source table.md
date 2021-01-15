---
keyword: [Message Queue for Apache Kafka, source table]
---

# Create a Message Queue for Apache Kafka source table

This topic describes how to create a Message Queue for Apache Kafka source table in Realtime Compute for Apache Flink. It also describes the mapping between the values of the type parameter and Kafka versions, and provides examples on how to parse messages of Message Queue for Apache Kafka.

**Note:**

-   This topic applies only to Blink 2.0 and later.
-   This topic applies only to Realtime Compute for Apache Flink in exclusive mode.
-   You can use Realtime Compute for Apache Flink to read data from a source table of a self-managed Kafka cluster. Before data is read, you must pay attention to the mapping between the values of the type parameter and Kafka versions, and the network configurations of the self-managed Kafka cluster and your Realtime Compute for Apache Flink cluster.

## Introduction to the Message Queue for Apache Kafka source table

Message Queue for Apache Kafka is a distributed, high-throughput, and scalable message queue service that is provided by Alibaba Cloud. Message Queue for Apache Kafka is widely used in big data scenarios, such as log collection, monitoring data aggregation, streaming data processing, and online and offline data analysis. Realtime Compute for Apache Flink can use Message Queue for Apache Kafka tables as source tables or result tables for the processing of streaming data.

The output data of Message Queue for Apache Kafka is in the serialized VARBINARY format. For each data record obtained from the Message Queue for Apache Kafka source table, you must compile a user-defined table-valued function \(UDTF\) to parse the data into a data structure before serialization. Realtime Compute for Apache Flink first extracts data from a Message Queue for Apache Kafka source table, compiles a UDTF to parse the data, and then exports the result data to a sink. Flink SQL also allows you to use the CAST function to parse data of the VARBINARY type into data of the VARCHAR type. For more information about UDTFs, see [UDTF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDTF.md).

## DDL syntax

The DDL definition of a Message Queue for Apache Kafka source table must be the same as the DDL definition in the following SQL statement. You can modify the settings of the parameters in the WITH clause.

```
create table kafka_stream(   --The sequence and data types of the following fields must be the same as the five fields in the Message Queue for Apache Kafka source table.
  messageKey VARBINARY,
  `message`    VARBINARY,
  topic      VARCHAR,
  `partition`  INT,
  `offset`     BIGINT        
) with (
  type ='kafka010',
  topic = '<yourTopicName>',
  `group.id` = '<yourGroupId>',
  ...
);
```

## Parameters in the WITH clause

-   General configurations

    |Parameter|Description|Required|Remarks|
    |---------|-----------|--------|-------|
    |type|The Kafka version.|Yes|Valid values: Kafka08, Kafka09, Kafka010, and Kafka011. For more information about the mapping between the values of the type parameter and Kafka versions, see [Mapping between the values of the type parameter and Kafka versions](#section_o4c_b4z_bgb).|
    |topic|The name of the topic to read.|Yes|None.|
    |topicPattern|The regular expression used to read multiple topics.|No|Topics are separated by vertical bars \(\|\), for example, `topic1|topic2|topic3`. For more information, see [Class Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html).|
    |startupMode|The start offset for reading data.|No **Note:** We recommend that you set this parameter based on your business requirements.

|Valid values:     -   GROUP\_OFFSETS \(default value\): reads data by group.
    -   EARLIEST: reads data from the earliest offset in a Kafka cluster.
    -   LATEST: reads data from the latest offset in a Kafka cluster.
    -   TIMESTAMP: reads data from a specified time point.

You can specify the startTimeMs or startTime parameter in the WITH clause. The value of the startTimeMs parameter is a timestamp, whereas the value of the startTime parameter is in the `yyyy-MM-dd HH:mm:ss` format. We recommend that you use the startTimeMs parameter. If neither of the parameters is specified, data is consumed from the start offset specified by Realtime Compute for Apache Flink.

**Note:**

    -   If you do not specify this parameter in plaintext, GROUP\_OFFSETS is used by default.
    -   If you set this parameter to TIMESTAMP, you must specify the time zone in job parameters in plaintext, for example, `blink.job.timeZone=Asia/Shanghai`.
    -   In GROUP\_OFFSETS mode, if no offset is configured, data is read from the end of a Kafka partition when a group consumes Kafka data for the first time.
    -   Only Kafka010 and Kafka011 support the TIMESTAMP data type. |
    |partitionDiscoveryIntervalMS|The interval at which new partitions are checked.|No|    -   Kafka08: This feature is enabled by default.
    -   Kafka09 and later: This feature takes effect only after you specify this parameter in the WITH clause in plaintext.
Unit: milliseconds. Default value: 60000 \(1 minute\). |
    |extraConfig|Additional KafkaConsumer configuration items.|No|You want to add configuration items that are required in special scenarios but are not included in the optional configuration items. Example: `'fetch.message.max.bytes=104857600'`. Multiple configuration items are separated by semicolons \(;\).|

-   Configurations for Kafka08
    -   Required parameters

        |Parameter|Description|Required|
        |---------|-----------|--------|
        |group.id|The ID of a consumer group.|Yes|
        |zookeeper.connect|The ZooKeeper URL.|Yes|

    -   Optional configurations
        -   consumer.id
        -   socket.timeout.ms
        -   fetch.message.max.bytes
        -   num.consumer.fetchers
        -   auto.commit.enable
        -   auto.commit.interval.ms
        -   queued.max.message.chunks
        -   rebalance.max.retries
        -   fetch.min.bytes
        -   fetch.wait.max.ms
        -   rebalance.backoff.ms
        -   refresh.leader.backoff.ms
        -   auto.offset.reset
        -   consumer.timeout.ms
        -   exclude.internal.topics
        -   partition.assignment.strategy
        -   client.id
        -   zookeeper.session.timeout.ms
        -   zookeeper.connection.timeout.ms
        -   zookeeper.sync.time.ms
        -   offsets.storage
        -   offsets.channel.backoff.ms
        -   offsets.channel.socket.timeout.ms
        -   offsets.commit.max.retries
        -   dual.commit.enabled
        -   partition.assignment.strategy
        -   socket.receive.buffer.bytes
        -   fetch.min.bytes
-   Configurations for Kafka09, Kafka010, and Kafka011
    -   Required configurations for Kafka09, Kafka010, and Kafka011

        |Parameter|Description|
        |---------|-----------|
        |group.id|The ID of a consumer group.|
        |bootstrap.servers|The endpoint for access to a Message Queue for Apache Kafka cluster.|

    -   For more information about the optional configurations for Kafka09, Kafka010, and Kafka011, see the following Kafka documentation:

        -   [Kafka09](https://kafka.apache.org/090/documentation.html?spm=a2c4g.11186623.2.18.5fda779biQ4ToG#newconsumerconfigs)
        -   [Kafka010](https://kafka.apache.org/0100/documentation.html?spm=a2c4g.11186623.2.18.5fda779biQ4ToG#newconsumerconfigs)
        -   [Kafka011](https://kafka.apache.org/0110/documentation.html?spm=a2c4g.11186623.2.17.5fda779biQ4ToG#consumerconfigs)
        If you want to modify the configurations, you can add parameters to the WITH clause in the DDL statement. For example, if you want to configure Simple Authentication and Security Layer \(SASL\), add the `security.protocol`, `sasl.mechanism`, and `sasl.jaas.config` parameters.

        ```
        create table kafka_stream(
          messageKey varbinary,
          `message` varbinary,
          topic varchar,
          `partition` int,
          `offset` bigint
        ) with (
          type ='kafka010',
          topic = '<yourTopicName>',
          `group.id` = '<yourGroupId>',
          ...,
          `security.protocol`=SASL_PLAINTEXT,
          `sasl.mechanism`=PLAIN,
          `sasl.jaas.config`='org.apache.kafka.common.security.plain.PlainLoginModule required username="<yourUserName>" password="<yourPassword>";'
        );
        ```


## Mapping between the values of the type parameter and Kafka versions

|type|Kafka version|
|----|-------------|
|Kafka08|0.8.22|
|Kafka09|0.9.0.1|
|Kafka010|0.10.2.1|
|Kafka011|0.11.0.2 and later|

## Examples of parsing messages of Message Queue for Apache Kafka

-   Scenario 1: Realtime Compute for Apache Flink processes the data read from Message Queue for Apache Kafka and exports the result data to ApsaraDB RDS.

    Data stored in Message Queue for Apache Kafka is in the JSON format and must be processed by using Realtime Compute for Apache Flink. The following code shows the message format:

    ```
    {
      "name":"Alice",
      "age":13,
      "grade":"A"
    }                
    ```

    -   Data processing method 1: Realtime Compute for Apache Flink reads and processes data from the Message Queue for Apache Kafka source table and then exports the result data to ApsaraDB RDS.

        In Blink 2.2.7 and later, you can use the CAST function to convert the VARBINARY data type to the VARCHAR data type. Then, use the JSON\_VALUE function to parse the data of the Message Queue for Apache Kafka source table. The following code shows an example:

        ```
        CREATE TABLE kafka_src (
          messageKey  VARBINARY,
          `message`   VARBINARY,
          topic       VARCHAR,
          `partition` INT,
          `offset`    BIGINT
        ) WITH (
          type = 'kafka010', -- For more information, see Mapping between the values of the type parameter and Kafka versions.
        );
        
        CREATE TABLE rds_sink (
          `name`       VARCHAR,
          age         VARCHAR,
          grade       VARCHAR
        ) WITH(
          type='rds'
        );
        
        CREATE VIEW input_view AS 
          SELECT CAST(`message` as VARCHAR ) as `message`
        FROM kafka_src;
        
        INSERT INTO rds_sink
        SELECT 
          JSON_VALUE(`message`,'$.name'),
          JSON_VALUE(`message`,'$.age'),
          JSON_VALUE(`message`,'$.grade')
        FROM input_view;
        ```

    -   Data processing method 2: Realtime Compute for Apache Flink first extracts data from the Message Queue for Apache Kafka source table, compiles a UDTF to parse the data, and then exports the result data to ApsaraDB RDS.

        To parse irregular data or complex JSON data, you must compile UDTF code. Examples:

        -   SQL

            ```
            -- Define a UDTF to parse messages of Message Queue for Apache Kafka.
            CREATE FUNCTION kafkaparser AS 'com.alibaba.kafkaUDTF';
            
            -- Define a Message Queue for Apache Kafka source table. Note that the fields declared in the DDL statement of the Message Queue for Apache Kafka source table must be the same as the fields in the following example. You can modify the settings of the parameters in the WITH clause.
            CREATE TABLE kafka_src (
              messageKey  VARBINARY,
              `message`   VARBINARY,
              topic       VARCHAR,
              `partition` INT,
              `offset`    BIGINT
            ) WITH (
              type = 'kafka010', -- For more information, see Mapping between the values of the type parameter and Kafka versions.
              topic = 'test_kafka_topic',
              `group.id` = 'test_kafka_consumer_group',
              bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            
            CREATE TABLE rds_sink (
              name       VARCHAR,
              age        INT,
              grade      VARCHAR,
              updateTime TIMESTAMP
            ) WITH(
              type='rds',
              url='jdbc:mysql://localhost:3306/test',
              tableName='test4',
              userName='test',
              password='<yourDatabasePassword>'
            );
            
            -- Use a UDTF to parse data of the VARBINARY data type into formatted data.
            CREATE VIEW input_view (
              name,
              age,
              grade,
              updateTime
            ) AS
            SELECT
              T.name,
              T.age,
              T.grade,
              T.updateTime
            FROM
              kafka_src as S,
              LATERAL TABLE (kafkaparser (`message`)) as T (
              name,
              age,
              grade,
              updateTime
            );
            
            -- Compute the formatted data and export the result to ApsaraDB RDS.
            INSERT INTO rds_sink
            SELECT 
              name,
              age,
              grade,
              updateTime
            FROM input_view;                                
            ```

        -   UDTF

            For more information about how to create a UDTF, see [UDTF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDTF.md). The following example shows Maven dependencies of Blink 2.2.4.

            ```
                <dependencies>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-core</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-java_2.11</artifactId>
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
                        <groupId>com.alibaba</groupId>
                        <artifactId>fastjson</artifactId>
                        <version>1.2.9</version>
                    </dependency>
                </dependencies>
            ```

            ```
            package com.alibaba;
            
            import com.alibaba.fastjson.JSONObject;
            import org.apache.flink.table.functions.TableFunction;
            import org.apache.flink.table.types.DataType;
            import org.apache.flink.table.types.DataTypes;
            import org.apache.flink.types.Row;
            import java.io.UnsupportedEncodingException;
            import java.sql.Timestamp;
            
            public class kafkaUDTF extends TableFunction<Row> {
                public void eval(byte[] message) {
                    try {
                        /* input message :
                            {
                              "name":"Alice",
                              "age":13,
                              "grade":"A",
                              "updateTime":1544173862
                            }
                        */
                        String msg = new String(message, "UTF-8");
                        try {
                            JSONObject data = JSON.parseObject(msg);
                            if (data ! = null) {
                                String name = data.getString("name") == null ? "null" : data.getString("name");
                                Integer age = data.getInteger("age") == null ? 0 : data.getInteger("age");
                                String grade = data.getString("grade") == null ? "null" : data.getString("grade");
                                Timestamp updateTime = data.getTimestamp("updateTime");
            
                                Row row = new Row(4);
                                row.setField(0, name);
                                row.setField(1, age);
                                row.setField(2, grade);
                                row.setField(3,updateTime );
            
                                System.out.println("Kafka message str ==>" + row.toString());
                                collect(row);
                            }
                        } catch (ClassCastException e) {
                            System.out.println("Input data format error. Input data " + msg + "is not json string");
                        }
                    } catch (UnsupportedEncodingException e) {
                        e.printStackTrace();
                    }
                }
                @Override
                // If the return value is declared as a row, you must reload the UDTF method and specify the types of fields to be returned.
                public DataType getResultType(Object[] arguments, Class[] argTypes) {
                    return DataTypes.createRowType(DataTypes.STRING, DataTypes.INT, DataTypes.STRING, DataTypes.TIMESTAMP);
                }
            }                                
            ```

-   Scenario 2: Realtime Compute for Apache Flink reads data from a Message Queue for Apache Kafka source table and then processes the data by using window functions.

    Based on the design of Realtime Compute for Apache Flink, you must define watermarks in the DDL statement of a source table for windows such as tumbling and sliding windows. For more information, see [Watermark](/intl.en-US/Exclusive Mode/Flink SQL/Basic concepts/Watermark.md). The method you use to define a watermark in a Message Queue for Apache Kafka source table is different from the method you use for other types of source tables. If you need to perform an event time-based computation by using a window function, you must use a user-defined extension \(UDX\) to parse the event time in the message field of a source table. Then, you can define a watermark based on the parsed event time. You must use a [Computed column](/intl.en-US/Exclusive Mode/Flink SQL/Basic concepts/Computed column.md) to convert data types for the event time parsed from a Message Queue for Apache Kafka source table. Assume that the data `2018-11-11 00:00:00|1|Anna|female` is written into the Message Queue for Apache Kafka source table. During the computing process, Realtime Compute for Apache Flink first extracts data from the Message Queue for Apache Kafka source table, compiles a UDTF to parse the data, and then exports the result data to ApsaraDB RDS.

    -   Data processing method 1: Realtime Compute for Apache Flink reads and processes data from the Message Queue for Apache Kafka source table and then exports the result data to ApsaraDB RDS.

        In Blink 2.2.7 and later, you can use the CAST function to convert the VARBINARY data type to the VARCHAR data type. Then, use the JSON\_VALUE function to parse the data of the Message Queue for Apache Kafka source table. The following code shows an example:

        ```
        CREATE TABLE kafka_src (
          messageKey VARBINARY,
          `message` VARBINARY,
          topic VARCHAR,
          `partition` INT,
          `offset` BIGINT,
          ts as to_timestamp(json_value(cast(`message` as VARCHAR ),'$.nodes.time')),
          WATERMARK wk FOR ts as withOffset(ts, 2000)
        ) WITH (type = 'kafka' -- For more information, see Mapping between the values of the type parameter and Kafka versions.
        );
        
        CREATE TABLE rds_sink (
          starttime TIMESTAMP ,
          endtime   TIMESTAMP ,
          `message` BIGINT 
        ) WITH (type = 'rds');
        
        INSERT
          INTO rds_sink
        SELECT
          TUMBLE_START(ts, INTERVAL '1' MINUTE),
          TUMBLE_END(ts, INTERVAL '1' MINUTE),
          count(`message`)
        FROM
          kafka_src
        GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE);
        ```

    -   Data processing method 2: Realtime Compute for Apache Flink first extracts data from the Message Queue for Apache Kafka source table, compiles a UDTF to parse the data, and then exports the result data to ApsaraDB RDS.
        -   SQL

            ```
            -- Define a UDTF to parse messages of Message Queue for Apache Kafka.
            CREATE FUNCTION kafkapaser AS 'com.alibaba.kafkaUDTF';
            CREATE FUNCTION kafkaUDF AS 'com.alibaba.kafkaUDF';
            
            -- Define a Message Queue for Apache Kafka source table. Note that the fields defined in the DDL statement must be the same as the fields in the following statement. You can modify the settings of the parameters in the WITH clause.
            create table kafka_src (
              messageKey VARBINARY,
              `message` VARBINARY,
              topic VARCHAR,
              `partition` INT,
              `offset` BIGINT,
              ctime AS TO_TIMESTAMP(kafkaUDF(`message`)), -- Define a computed column. A computed column can be considered as a placeholder column that is not stored in a source table. The values in this column are computed. If you want to define a watermark, the data type of the computed column must be TIMESTAMP.
              watermark for `ctime` as withoffset(`ctime`,0) -- Define a watermark in a computed column.
            ) WITH (
              type = 'kafka010', -- For more information, see Mapping between the values of the type parameter and Kafka versions.
              topic = 'test_kafka_topic',
              `group.id` = 'test_kafka_consumer_group',
              bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            
            create table rds_sink (
              `name` VARCHAR,
              age INT,
              grade VARCHAR,
              updateTime TIMESTAMP
            ) WITH(
              type='rds',
              url='jdbc:mysql://localhost:3306/test',
              tableName='test4',
              userName='test',
              password='<yourPassword>'
            );
            
            
            -- Use a UDTF to parse data of the VARBINARY data type to formatted data.
            CREATE VIEW input_view AS
            SELECT
              S.ctime,
              T.`order`,
              T.`name`,
              T.sex
              from
              kafka_src as S,
              LATERAL TABLE (kafkapaser (`message`)) as T (
              ctime,
              `order`,
              `name`,
              sex
            );
            
            -- Compute the data in input_view.
            CREATE VIEW view2 (
              cnt,
              sex
            ) AS
              SELECT
              COUNT(*) as cnt,
              T.sex
              from
              input_view
            Group BY sex, TUMBLE(ctime, INTERVAL '1' MINUTE);
            
            -- Compute the formatted data and export the result to ApsaraDB RDS.
            insert into rds_sink
              SELECT
              cnt,sex
            from view2;
            ```

        -   UDF&UDTF

            For more information about how to create UDFs and UDTFs, see [UDF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDF.md) and [UDTF](/intl.en-US/Exclusive Mode/Flink SQL/UDX/UDTF.md). The following example shows Maven dependencies of Blink 2.2.4.

            ```
              <dependencies>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-core</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-java_2.11</artifactId>
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
                        <groupId>com.alibaba</groupId>
                        <artifactId>fastjson</artifactId>
                        <version>1.2.9</version>
                    </dependency>
                </dependencies>                                
            ```

            -   UDTF

                ```
                package com.alibaba;
                
                import com.alibaba.fastjson.JSONObject;
                import org.apache.flink.table.functions.TableFunction;
                import org.apache.flink.table.types.DataType;
                import org.apache.flink.table.types.DataTypes;
                import org.apache.flink.types.Row;
                import java.io.UnsupportedEncodingException;
                
                /**
                  The following example demonstrates how to parse the JSON strings in a Message Queue for Apache Kafka source table and format the parsed data.
                **/
                public class kafkaUDTF extends TableFunction<Row> {
                
                    public void eval(byte[] message) {
                        try {
                          // Read data of the VARBINARY data type and convert it to the STRING data type.
                            String msg = new String(message, "UTF-8");
                
                                // Extract data from JSON strings based on the following fields.
                                    String ctime = Timestamp.valueOf(data.split('\\|')[0]);
                                    String order = data.split('\\|')[1];
                                    String name = data.split('\\|')[2];
                                    String sex = data.split('\\|')[3];
                
                                    // Return rows of data based on the parsed fields.
                                    Row row = new Row(4);
                                    row.setField(0, ctime);
                                    row.setField(1, age);
                                    row.setField(2, grade);
                                    row.setField(3, updateTime);
                
                                    System.out.println("Kafka message str ==>" + row.toString());
                
                                    // Return a row of data.
                                    collect(row);
                
                            } catch (ClassCastException e) {
                                System.out.println("Input data format error. Input data " + msg + "is not json string");
                            }
                
                
                        } catch (UnsupportedEncodingException e) {
                            e.printStackTrace();
                        }
                
                    }
                
                    @Override
                    // If the return value is declared as a row, you must reload the UDTF method and specify the types of fields to be returned.
                    // Define the data types for objects in output rows.
                    public DataType getResultType(Object[] arguments, Class[] argTypes) {
                        return DataTypes.createRowType(DataTypes.TIMESTAMP,DataTypes.STRING, DataTypes.Integer, DataTypes.STRING,DataTypes.STRING);
                    }
                
                }
                ```

            -   UDF

                ```
                package com.alibaba;
                package com.hjc.test.blink.sql.udx;
                import org.apache.flink.table.functions.FunctionContext;
                import org.apache.flink.table.functions.ScalarFunction;
                
                public class KafkaUDF extends ScalarFunction {
                    // The open method is optional.
                    // To implement the open method, you must add "import org.apache.flink.table.functions.FunctionContext;" to the code.
                
                    public String eval(byte[] message) {
                
                         // Read data of the VARBINARY data type and convert it to the STRING data type.
                        String msg = new String(message, "UTF-8");
                        return msg.split('\\|')[0];
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


## Create a source table of a self-managed Kafka database

-   Sample code

    ```
    create table kafka_stream(
      messageKey VARBINARY,
      `message` VARBINARY, 
      topic varchar,
      `partition` int,
      `offset` bigint
    ) with (
      type ='kafka011',
      topic = 'kafka_01',
      `group.id` = 'CID_blink',
      bootstrap.servers = '192.168.0.251:****'
    );                
    ```

-   Parameters in the WITH clause

    For more information, see [Parameters in the WITH clause](#section_ivk_14z_bgb).

    **Note:**

    -   bootstrap.servers specifies the address and port number of a self-managed Kafka cluster.
    -   Only Blink 2.2.6 and later versions support the display of metrics such as transactions per second \(TPS\) and requests per second \(RPS\) of Message Queue for Apache Kafka or a self-managed Kafka database.

## FAQ

-   Q: What do I do if the following error occurs when a job is being started?

    ```
    ERR_ID:
         SQL-00010007
    CAUSE:
         Could not create table 'kafka_source' as source table
    ACTION:
         Please refer to details section for hint.
         If it doesn't help, please contact customer support
    DETAIL:
         java.lang.IllegalArgumentException: Startup time[1566481803000] must be before current time[1566453003356].
    ```

    A: This error is caused by invalid time zone settings. To solve this issue, add the following parameter to job parameters:

    ```
    blink.job.timeZone=Asia/Shanghai
    ```

-   Q: What do I do if a self-managed Kafka cluster cannot consume data?

    A:

    -   Causes

        Each broker of the Kafka cluster sends its meta information to ZooKeeper. Then, Kafka consumer accesses the brokers to extract data by using the endpoint in listener\_security\_protocol in the meta information of the brokers. The endpoint is either the IP address or the local server domain name and port number. If the machine where your Realtime Compute for Apache Flink job resides cannot access the endpoint, the consumer of connector cannot extract data. As a result, the data consumption process stops.

    -   Troubleshooting
        1.  View the endpoint for listener\_security\_protocol of the broker of ZooKeeper.

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4089493061/p64421.jpg)

        2.  Use the network detection feature to check whether you can access the IP address or domain name in the endpoint.
        3.  Log on to your machine to confirm the cause.
    -   Solution
        -   If the endpoint is an IP address, check whether a whitelist is configured for the Kafka server. For more information, see [Configure a whitelist for accessing storage resources](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md). If no whitelist is configured, configure a whitelist and try again.
        -   Exclusive clusters of Realtime Compute for Apache Flink cannot resolve domain names. If the endpoint is a domain name and a whitelist has been configured, use one of the following methods:
            -   If you cannot restart the Kafka service, perform the following operations:

                Purchase [PrivateZone of Alibaba Cloud DNS](https://www.aliyun.com/product/pvtz?spm=5176.10695662.1395782.1.305e10614trdiP) and configure domain name resolution for all Kafka brokers. After network detection based on the domain name is successful, restart your Realtime Compute for Apache Flink job.

            -   If you can restart the Kafka service, perform the following operations:

                Configure the IP address and port number \(in boostrap.servers in typical cases\) for advertised.listeners for the related broker. Make sure that the network connection is normal. Then, restart your Realtime Compute for Apache Flink job.

-   Q: Why does a job terminate immediately after it is started during the consumption of Kafka data?

    A: For versions earlier than Blink 3.3.0, if the startupMode parameter is set to TIMESTAMP and no data exists in all partitions of Kafka, the Kafka connector determines that no partition data can be consumed and terminates the job. You can view log information similar to `Consumer subtask {} initially has no partitions to read from.` in the `TaskManager.log` log file that corresponds to the job. We recommend that you upgrade the Blink version to 3.3.0 or later.

-   Q: What are the functions of the commit offset mechanism in Realtime Compute for Apache Flink?

    A: Realtime Compute for Apache Flink uses commitOffsetOnCheckpointing by default. The commit offset policy configured by users does not take effect. After checkpoint generation is enabled, Realtime Compute for Apache Flink commits the currently consumed offset to Kafka each time a checkpoint is generated. This way, data is consumed from the offset committed from the last checkpoint during job restoration. This ensures exactly-once processing of streaming data. If the checkpoint interval exceeds the specified upper limit, Kafka may fail to query the consumed offset.


