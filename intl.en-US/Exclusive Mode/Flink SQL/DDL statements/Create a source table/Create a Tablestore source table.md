---
keyword: [Tablestore, Tablestore source table]
---

# Create a Tablestore source table

This topic describes how to create a Tablestore source table in Realtime Compute for Apache Flink.

**Note:** This topic applies only to Realtime Compute for Apache Flink V3.2.2 and later.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service that is built on the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts data sharding and load balancing technologies to scale out and handle concurrent transactions. You can use Tablestore to store and query a large amount of structured data in real time.

## Tunnel Service of Tablestore

Tunnel Service is a centralized service that uses the Tablestore API to allow you to consume full and incremental data. You can use the Tunnel Service API and SDKs to create tunnels from which you can consume distributed data in real time. The distributed data is divided into the following types: incremental data, full data, and full and incremental data. You can use the tunnels of the Tunnel Service to consume existing data or added data in tables in streaming processing mode. In Realtime Compute for Apache Flink, the tunnels of the Tunnel Service can serve as the source of streaming data. Each data record uses a JSON-like format. You can run the following code if you need to use Tunnel Service tunnels:

```
{
            "OtsRecordType": "PUT",  // The data operation type, such as PUT, UPDATE, and DELETE.
            "OtsRecordTimestamp": 1506416585740836, // The time when the data is written. The time unit is milliseconds. The value 0 indicates that full data is written.
            "PrimaryKey": [
                {
                    "ColumnName": "pk_1", // The first primary key column.
                    "Value": 1506416585881590900
                },
                {
                    "ColumnName": "pk_2", // The second primary key column.
                    "Value": "string_pk_value"
                }
            ],
            "Columns": [
                {
                    "OtsColumnType": "Put", // The operation type for the column, such as PUT, DELETE_ONE_VERSION, and DELETE_ALL_VERSION.
                    "ColumnName": "attr_0",
                    "Value": "hello_table_store",
                },
                {
                    "OtsColumnType": "DELETE_ONE_VERSION", // No value is specified for the delete operation.
                    "ColumnName": "attr_1"
                }
            ]
}
```

## DDL syntax

In Realtime Compute for Apache Flink, you can use Tablestore to store input data. The following code shows an example:

```
create table tablestore_stream(
  pk_1 BIGINT,
  pk_2 VARCHAR,
  attr_0 VARCHAR,
  attr_1 DOUBLE,
  OtsRecordType  VARCHAR  HEADER //You must add HEADER to the attribute field.
) with (
  type ='ots',
  endPoint ='http://blink-demo.cn-hangzhou.vpc.tablestore.aliyuncs.com',
  instanceName = 'blink-demo',
  tableName ='demo_table',
  tunnelName = 'blink-demo-stream',
  accessId ='<yourAccessID>',
  accessKey ='<yourAccessSecret>',
  ignoreDelete = 'false' // Specifies whether to ignore the delete operation.
);        
```

## Attribute fields

For more information about how to obtain and use attribute fields in a Tablestore source table, see [Obtain attribute fields of a source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md).

|Field|Description|
|-----|-----------|
|`OtsRecordType`|The data operation type.|
|`OtsRecordTimestamp`|The data operation time. If you set this parameter to 0, full data is written.``|
|`<Column name>_OtsColumnType`|The operation type for a column.|

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the connector.|Set the value to `ots`.|
|endPoint|The endpoint of the Tablestore instance.|Enter the VPC endpoint if the instance is deployed in a VPC.|
|instanceName|The name of the Tablestore instance.|None.|
|tableName|The name of the Tablestore table.|Realtime Compute for Apache Flink does not reread data that has been read from a Tablestore source table. If you want Realtime Compute for Apache Flink to reread full data from the source table, you must create a data tunnel.|
|tunnelName|The tunnel name of the Tablestore source table.|None.|
|accessId|The AccessKey ID that is used to access Tablestore.|None.|
|accessKey|The AccessKey secret that is used to access Tablestore.|None.|
|ignoreDelete|Specifies whether to ignore the delete operation.|Optional. Default value: `false`.|

