# Create a Kafka source table {#concept_86824_zh .concept}

This topic describes how to create a Kafka source table in Realtime Compute. It also describes the Kafka version mapping and Kafka message parsing examples.

**Note:** This topic applies only to Realtime Compute deployed in exclusive mode.

## Introduction to Kafka source tables {#section_mqr_zmz_bgb .section}

Kafka source tables are implemented based on Kafka community edition. The data parsing process of a Kafka source table is Kafka source table -\> UDTF -\> Realtime Compute -\> sink. All data read from Kafka is in the VARBINARY \(binary\) format. You need to use a UDTF to parse VARBINARY data into formatted data.

## DDL definition {#section_zyh_dnz_bgb .section}

The DDL definition of the Kafka source table must be the same as that in the following SQL statement. The five fields in the table must use the following order.

```language-sql
-- Define the source table. Note that the DDL fields of the Kafka source table must be the same as those in the following example. WITH parameters are modifiable.
create table kafka_stream(
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

## WITH parameters {#section_ivk_14z_bgb .section}

-   General configuration

    |Name|Description|Remarks|
    |----|-----------|-------|
    |type|The Kafka version name.|Required. Valid values: Kafka08, Kafka09, Kafka010, and Kafka011. For the version mapping, see Mapping between Kafka version names and version numbers.|
    |topic|The topic read.|None|
    |topicPattern|The expression for reading multiple topics at a time.|None|
    |startupMode|The start offset.|     -   EARLIEST: reads data from the earliest Kafka partition.
    -   Group\_OFFSETS: reads data by group.
    -   LATEST: reads data from the latest Kafka checkpoint.
    -   TIMESTAMP: reads data from the specified checkpoint. This value is supported by Kafka010 and Kafka011.
 |
    |partitionDiscoveryIntervalMS|The interval for checking whether any new partition is generated. Unit: millisecond.|Default value: 60000, indicating 1 minute.|
    |extraConfig|The additional kafkaConsumer configuration items.|Optional. You can set configuration items that are required in special occasions but are not included in the optional configuration items.|

-   Required configuration for Kafka08

    |Name|Description|Remarks|
    |----|-----------|-------|
    |group.id|The name of the consumer group.|The ID of the consumer group.|
    |zookeeper.connect|The ZooKeeper URL.|The ZooKeeper connection ID.|

-   \(Optional\) Key
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
-   Required configuration for Kafka09, Kafka010, and Kafka011

    |Name|Description|Remarks|
    |----|-----------|-------|
    |group.id|The name of the consumer group.|The ID of the consumer group.|
    |bootstrap.servers|The Kafka cluster address.|None|

    For more information about other optional configuration items, see Kafka official documentation.

    -   [Kafka09](https://kafka.apache.org/0110/documentation.html#consumerconfigs)
    -   [Kafka010](https://kafka.apache.org/090/documentation.html#newconsumerconfigs)
    -   [Kafka011](https://kafka.apache.org/0102/documentation.html#newconsumerconfigs)
    When you need to configure any items, add the corresponding parameters in the WITH section of the DDL statement. For example, when you configure the SASL logon, you need to add the \`security.protocol\`, \`sasl.mechanism\`, and \`sasl.jaas.config\` parameters. The sample code is as follows:

    ```
    create table kafka_stream(
      messageKey varbinary,
      `message` varbinary,
      topic varchar,
      `partition`int,
      `offset`bigint
    ) with (
      type ='kafka010',
      topic = '<yourTopicName>',
      `group.id` = '<yourGroupId>',
      ...,
      `security.protocol`=SASL_PLAINTEXT,
      `sasl.mechanism`=PLAIN,
      `sasl.jaas.config`='org.apache.kafka.common.security.plain.PlainLoginModule required username="USERNAME" password="PASSWORD";'-- Enter the actual username and password, respectively.
    );
    ```


## Mapping between Kafka version names and version numbers {#section_o4c_b4z_bgb .section}

|Kafka version name|Kafka version number|
|------------------|--------------------|
|Kafka08|V0.8.22|
|Kafka09|V0.9.0.1|
|Kafka010|V0.10.2.1|
|Kafka011|V0.11.0.2|

## Kafka message parsing examples {#section_ycd_g4z_bgb .section}

-   Example 1:
    -   Scenario

        Assume that you want to compute Kafka data and write the output data to RDS. Kafka data is stored in JSON format and must be computed by Realtime Compute. The message format is as follows:

        ```language-json
        {
          "name":"Alice",
          "age":13,
          "grade":"A"
        }
        ```

        The entire computing process is Kafka source table -\> UDTF -\> Realtime Compute -\> RDS sink.

    -   Sample code
        -   SQL

            ```language-sql
            -- Define a UDTF that parses Kafka messages.
            CREATE FUNCTION kafkapaser AS 'com.alibaba.kafkaUDTF';
            
            -- Define the source table. Note that the DDL fields of the Kafka source table must be the same as those in the following example. WITH parameters are modifiable.
            create table kafka_src (
                messageKey  VARBINARY,
                `message`   VARBINARY,
                topic       VARCHAR,
                `partition` INT,
                `offset`    BIGINT
            ) WITH (
                type = 'kafka010',    -- The Kafka source type, which is strongly related to the Kafka version. For the version mapping, see Mapping between Kafka version names and version numbers.
                topic = 'test_kafka_topic',
                `group.id` = 'test_kafka_consumer_group',
                bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            create table rds_sink (
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
            
            -- Use the UDTF to parse binary data into formatted data.
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
            from
                kafka_src as S,
                LATERAL TABLE (kafkapaser (`message`)) as T (
                    name,
                    age,
                    grade,
                    updateTime
                );
            
            -- Compute the formatted data and write the output data to RDS.
            insert into rds_sink
              SELECT 
                  name,
                  age,
                  grade,
                  updateTime
              from input_view;
            									
            ```

        -   UDTF

            **Note:** The Flink version of the following Maven dependencies is determined by the Realtime Compute version of your job. For example, when you run a job in Realtime Compute V2.2.4, the Flink version of Maven dependencies is blink-2.2.4-SNAPSHOT. For more information about the download addresses of the dependency packages, see the [Build the development environment](intl.en-US/Flink SQL Development Guide/Flink SQL/UDX/UDX overview.md#section_ck2_gcm_cgb) section of the UDX overview topic. Maven dependencies:

            ```language-java
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

            ```language-java
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
                // If the return value is declared as Row, you must reload the getResultType method to explicitly inform the system of the returned field types.
                public DataType getResultType(Object[] arguments, Class[] argTypes) {
                    return DataTypes.createRowType(DataTypes.STRING, DataTypes.INT, DataTypes.STRING, DataTypes.TIMESTAMP);
                }
            }
            									
            ```

-   Example 2:
    -   Scenario

        Window computation is required for data read from Kafka. In the current design of Realtime Compute, to perform operations related to windows such as tumbling windows and sliding windows, you must define a watermark in the DDL statement of the source table. The Kafka source table is special. To perform window operations based on the Event Time of the message field in Kafka, you must first use a UDX to parse the Event Time from the message field, and then define a watermark. You need to use a computed column for the Kafka source table. Assume that data written to Kafka is as follows:

        `2018-11-11 00:00:00|1|Anna|female`. The entire computing process is Kafka source table -\> UDTF -\> Realtime Compute -\> RDS sink.

    -   Sample code
        -   SQL

            ```language-sql
            -- Define a UDTF that parses Kafka messages.
            CREATE FUNCTION kafkapaser AS 'com.alibaba.kafkaUDTF';
            CREATE FUNCTION kafkaUDF AS 'com.alibaba.kafkaUDF';
            
            -- Define the source table. Note that the DDL fields of the Kafka source table must be the same as those in the following example. WITH parameters are modifiable.
            create table kafka_src (
                messageKey  VARBINARY, 
                `message`   VARBINARY,
                topic       VARCHAR, 
                `partition` INT,
                `offset`    BIGINT,
                ctime AS TO_TIMESTAMP(kafkaUDF(`message`)), -- Define a computed column. The computed column can be understood as a placeholder. It does not exist in the source table. Data of the computed column can be computed at the downstream operator. Note that the data type of the computed column must be TIMESTAMP if you want to define a watermark.
                watermark for `ctime` as withoffset(`ctime`,0) -- Define a watermark based on the computed column.
            ) WITH (
                type = 'kafka010',    -- The Kafka source type, which is strongly related to the Kafka version. For the version mapping, see Mapping between Kafka version names and version numbers.
                topic = 'test_kafka_topic',
                `group.id` = 'test_kafka_consumer_group',
                bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            
            create table rds_sink (
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
            
            -- Use the UDTF to parse binary data into formatted data.
            CREATE VIEW input_view (
                name,
                age,
                grade,
                updateTime
            ) AS
            SELECT
                COUNT(*) as cnt,
                T.ctime,
                T.order,
                T.name,
                T.sex
            from
                kafka_src as S,
                LATERAL TABLE (kafkapaser (`message`)) as T (
                    ctime,
                    order,
                    name,
                    sex
                )
            Group BY T.sex,
                    TUMBLE(ctime, INTERVAL '1' MINUTE);
            
            -- Compute the output data from input_view.
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
            
            
            
            -- Compute the formatted data and write the output data to RDS.
            insert into rds_sink
              SELECT 
                  cnt,sex
              from view2;
            ```

        -   UDF&UDTF

            **Note:** The Flink version of the following Maven dependencies is determined by the Realtime Compute version of your job. For example, when you run a job in Realtime Compute V2.2.4, the Flink version of Maven dependencies is blink-2.2.4-SNAPSHOT. For more information about the download addresses of the dependency packages, see the [Build the development environment](intl.en-US/Flink SQL Development Guide/Flink SQL/UDX/UDX overview.md#section_ck2_gcm_cgb) section of the UDX overview topic. Maven dependencies:

            ```language-java
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
                  The following example shows how to parse the input JSON strings of Kafka and generate formatted data.
                **/
                public class kafkaUDTF extends TableFunction<Row> {
                
                    public void eval(byte[] message) {
                        try {
                          // Read a binary data record and convert it to the STRING type.
                            String msg = new String(message, "UTF-8");
                
                                // Extract fields from the JSON object.
                                    String ctime = Timestamp.valueOf(data.split('\\|')[0]);
                                    String order = data.split('\\|')[1];
                                    String name = data.split('\\|')[2];
                                    String sex = data.split('\\|')[3];
                
                                    // Place the formatted fields into the Row() object for output.
                                    Row row = new Row(4);
                                    row.setField(0, ctime);
                                    row.setField(1, age);
                                    row.setField(2, grade);
                                    row.setField(3, updateTime);
                
                
                                    System.out.println("Kafka message str ==>" + row.toString()); 
                
                                    // Generate a row.
                                    collect(row);
                
                
                            } catch (ClassCastException e) {
                                System.out.println("Input data format error. Input data " + msg + "is not json string");
                            }
                
                
                        } catch (UnsupportedEncodingException e) {
                            e.printStackTrace();
                        }
                
                    }
                
                    @Override
                    // If the return value is declared as Row, you must reload the getResultType method to explicitly inform the system of the returned field types.
                    // Define the field type of the Row() object for output.
                    public DataType getResultType(Object[] arguments, Class[] argTypes) {
                        return DataTypes.createRowType(DataTypes.TIMESTAMP,DataTypes.STRING, DataTypes.Integer, DataTypes.STRING,DataTypes.STRING);
                    }
                
                }
                											
                ```

            -   UDF

                ```language-java
                package com.alibaba;
                package com.hjc.test.blink.sql.udx;
                import org.apache.flink.table.functions.FunctionContext;
                import org.apache.flink.table.functions.ScalarFunction;
                
                
                public class KafkaUDF extends ScalarFunction {
                    // The open method is optional.
                    // You need to run import org.apache.flink.table.functions.FunctionContext;.
                
                    public String eval(byte[] message) {
                
                         // Read a binary data record and convert it to the STRING type.
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


Self-built Kafka

-   Examples

    ```language-sql
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

-   WITH parameters

    For more information about WITH parameters of self-built Kafka, see descriptions of WITH parameters in this topic. Note that you must enter the address and port number of your self-built Kafka for the `bootstrap.servers` parameter.

    **Note:** Only Realtime Compute V2.2.6 and later support displaying metric information such as TPS and RPS of Alibaba Cloud Kafka or your self-built Kafka.


