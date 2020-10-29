# Create an InfluxDB result table

This topic describes how to create an InfluxDB result table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause and data type mapping involved when you create such a result table.

**Note:**

-   InfluxDB does not support the storage registration feature.
-   This topic applies only to Blink 3.5.0-hotfix and later.

## DDL syntax

In Realtime Compute for Apache Flink, you can use InfluxDB to store output data. The following code shows an example:

```
create table stream_test_influxdb(
    `metric` varchar,
    `timestamp` BIGINT,
    `tag_value1` varchar,
    `field_fieldValue1` Double
)with(
    type = 'influxdb',
    endpoint = 'http://service.cn.influxdb.aliyuncs.com:****',
    database = '<yourDatabaseName>',
    batchPutsize = '1',
    username = '<yourDatabaseUserName>',
    password = '<yourDatabasePassword>'
);
```

Default format for tables:

-   Column 0: metric \(VARCHAR\). This column is required.
-   Column 1: timestamp \(BIGINT\), in milliseconds. This column is required.
-   Column 2: tag\_value1 \(VARCHAR\). This column is required. You must enter at least one value in this column.
-   Column 3: field\_fieldValue1 \(DOUBLE\). This column is required. You must enter at least one value in this column.

    To enter multiple values of field\_fieldValue, use the following format:

    ```
    field_fieldValue1 Data type,
    field_fieldValue2 Data type,
    ...      
    field_fieldValueN Data type
    ```

    The following code shows an example:

    ```
    field_fieldValue1 Double,
    field_fieldValue2 INTEGER,
    ...      
    field_fieldValueN INTEGER
    ```


**Note:** An InfluxDB result table can contain only the metric, timestamp, tag\_\*, and field\_\* fields.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to InfluxDB.|
|endpoint|The endpoint of an InfluxDB database.|Yes|The endpoint of an InfluxDB database is the VPC endpoint of the InfluxDB database, for example, https://localhost:3242 or http://localhost:8086.

Endpoints support both HTTP and HTTPS. |
|database|The name of an InfluxDB database.|Yes|For example, db-blink or blink.|
|batchPutSize|The number of data records submitted at a time.|No|Default value: 500.|
|username|The username that is used to access the InfluxDB database.|Yes|You must have the write permission on the destination database.|
|password|The password that is used to access the InfluxDB database.|Yes|Default value: 0.|
|retentionPolicy|The retention policy.|No|If this parameter is empty, the default retention policy is used for each database.|

## Field type mapping

|InfluxDB data type|Data type of Realtime Compute for Apache Flink|
|------------------|----------------------------------------------|
|BOOLEAN|BOOLEAN|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|

