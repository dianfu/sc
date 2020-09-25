---
keyword: [Elasticsearch, ES, 维表]
---

# Elasticsearch维表

本文为您介绍Elasticsearch（ES）维表DDL定义、WITH参数、CACHE参数和代码示例。

## DDL定义

```
 CREATE TABLE es_stream_sink(
   field1 LONG, ---作为JOIN时的key，必须为STRING类型。
   field2 VARBINARY, 
   field3 VARCHAR
) WIHT (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);
```

**说明：**

-   维表JOIN时的key有且只能有1个，且为Elasticsearch对应索引的文档ID。
-   支持Elasticsearch 5.5及以上版本。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型|是|固定值为`elasticsearch`。|
|endPoint|Server地址|是|例如：http://127.0.0.1:9200。|
|accessId|AccessKey ID|否|无|
|accessKey|AccessKey Secret|否|无|
|indexName|文档索引名称|是|无|
|typeNames|Type名称|否|默认值为`_doc`。**说明：** Elasticsearch 7.0以上版本不建议设置该参数。 |

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|Elasticsearch仅支持以下两种缓存策略：-   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|否|选择`LRU`缓存策略后，可以设置缓存大小，默认值为10000行。|
|cacheTTLMs|缓存更新时间间隔|否|默认缓存不超时，单位为毫秒。不同缓存策略下的功能如下： -   LRU：设置缓存失效的超时时长。
-   ALL：设置缓存加载的间隔时长，默认不重新加载。 |
|cacheEmpty|是否缓存空结果。|否|默认值为true。|

## 类型映射

Flink全托管以JSON来解析Elasticsearch数据，详情请参见[数据类型映射关系](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/formats/json.html)。

## 代码示例

```
CREATE TABLE event (
   id STRING, 
   data STRING,
   proctime as PROCTIME()
) with (
   'connector' = 'xxx'
);

CREATE TABLE white_list (
   id STRING,
   `value` FLOAT
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);

SELECT e.*, w.*
  FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF T.proctime AS w
ON e.id = w.id;
```

