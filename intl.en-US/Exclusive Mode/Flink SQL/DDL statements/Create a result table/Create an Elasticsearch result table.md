# Create an Elasticsearch result table

This topic describes how to create an Elasticsearch result table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause used when you create such a result table.

**Note:** This topic applies only to Blink 3.2.2 and later.

## DDL syntax

In Realtime Compute for Apache Flink, you can use Elasticsearch to store output data. The following code shows an example:

```
 CREATE TABLE es_stream_sink(
  field1 LONG,
  field2 VARBINARY,
  field3 VARCHAR,
  PRIMARY KEY(field1)
)WITH(
  type ='elasticsearch',
  endPoint = 'http://es-cn-mp****.public.elasticsearch.aliyuncs.com:****',
  accessId = '<yourAccessId>',
  accessKey = '<yourAccessSecret>',
  index = '<yourIndex>',
  typeName = '<yourTypeName>'
);
```

**Note:**

-   Elasticsearch supports data updates based on the PRIMARY KEY field. You can specify only one field for the PRIMARY KEY field.
-   After you specify the PRIMARY KEY field, values in the field are used as document IDs.
-   If you do not specify the PRIMARY KEY field, document IDs are randomly generated. For more information, see [Index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html).
-   In full update mode, later documents overwrite earlier documents.
-   In incremental update mode, the system updates the fields based on the entered field values.
-   All updates use the UPSERT syntax by default, which means to insert or update data.

## Parameters in the WITH clause \(general configurations\)

|Parameter|Description|Default value|Required|
|---------|-----------|-------------|--------|
|type|The type of the connector.|elasticsearch|Yes|
|endPoint|The server address of an Elasticsearch cluster, for example, http://127.0.0.1:9211.|None|Yes|
|accessId|The AccessKey ID that is used to access an Elasticsearch cluster. **Note:** If you use the Kibana plug-in to connect to Elasticsearch, enter the Kibana logon ID.

|None|Yes|
|accessKey|The AccessKey secret that is used to access an Elasticsearch cluster. **Note:** If you use the Kibana plug-in to connect to Elasticsearch, enter the Kibana logon password.

|None|Yes|
|index|The index name, which is similar to the database name.|None|Yes|
|typeName|The type name, which is similar to the database table name.|None|Yes|
|bufferSize|The maximum number of data records that can be stored in the buffer before deduplication is triggered.|1000|No|
|maxRetryTimes|The maximum number of retries for writing data into a table.|30|No|
|timeout|The read timeout period. Unit: milliseconds.|600000|No|
|discovery|Specifies whether node discovery is enabled. If it is enabled, the client refreshes the server list every five minutes.|false|No|
|compression|Specifies whether to compress request bodies in the GZIP format.|true|No|
|multiThread|Specifies whether to enable multithreading for JestClient.|true|No|
|ignoreWriteError|Specifies whether to ignore write exceptions.|false|No|
|settings|The settings used to create indexes.|None|No|
|updateMode|The update mode used after the primary key is specified.-   full: Full data is overwritten.
-   inc: Incremental data is updated.

|full|No|

## Parameters in the WITH clause \(dynamic indexing\)

|Parameter|Description|Default value|Required|
|---------|-----------|-------------|--------|
|dynamicIndex|Specifies whether to enable dynamic indexing.-   true: Dynamic indexing is enabled.
-   false: Dynamic indexing is disabled.

|false|No|
|indexField|The field name of the index.|None.|This parameter is required when dynamicIndex is set to true. This parameter supports only the TIMESTAMP \(in seconds\), DATE, and LONG data types.|
|indexInterval|The interval at which an index is changed.|d|This parameter is required when dynamicIndex is set to true. Valid values: -   d: day
-   m: month
-   w: week |

**Note:**

-   Only Blink 2.2.7 and later versions support the dynamic indexing feature.
-   After dynamic indexing is enabled, the `index` name in the basic configuration is used as the unified alias for indexes created subsequently. An alias can correspond to multiple indexes.
-   Actual index names that correspond to different values of `indexInterval`:
    -   d -\> Alias + "yyyyMMdd"
    -   m -\> Alias + "yyyyMM"
    -   w -\> Alias + "yyyyMMW"
-   You can use Index API to change a single actual index, but the alias supports only the `GET` method. If you want to change the alias, see [Index Aliases](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html).

