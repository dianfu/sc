# 创建Elasticsearch维表

本文为您介绍如何创建实时计算Flink版Elasticsearch（ES）维表，以及创建维表时使用的WITH参数和CACHE参数。

**说明：** 本文仅适用于Blink 3.2.2及以上版本。

## DDL定义

实时计算Flink版支持使用ES作为维表，示例代码如下。

```
 CREATE TABLE es_stream_sink(
  field1 LONG, 
  field2 VARBINARY, 
  field3 VARCHAR,
  PRIMARY KEY(field1),
  PERIOD FOR SYSTEM_TIME
) WITH (
  type ='elasticsearch',
  endPoint = '<yourEndPoint>',
  accessId = '<yourAccessId>',
  accessKey = '<yourAccessSecret>',
  index = '<yourIndex>',
  typeName = '<yourTypeName>'
);
```

**说明：** ES维表支持根据ES的PRIMARY KEY进行PRIMARY KEYUPDATE，且PRIMARY KEY只能为1个字段。

## WITH参数

|参数|说明|默认值|是否必选|
|--|--|---|----|
|type|维表类型|elasticsearch|是|
|endPoint|Server地址，例如：http://127.0.0.1:9211。|无|是|
|accessId|访问实例ID|无|是|
|accessKey|访问实例密钥|无|是|
|index|索引名称，类似于数据库Database的名称。|无|是|
|typeName|Type名称，类似于数据库的Table名称。|无|是|
|maxRetryTimes|异常重试次数|30|否|
|timeout|读取超时时长，单位为毫秒。|600000|否|
|discovery|是否开启节点发现。如果开启，客户端每5分钟刷新一次Server List。|false|否|
|compression|是否使用GZIP压缩Request Bodies。|true|否|
|multiThread|是否开启JestClient多线程。|true|否|

## CACHE参数

|参数|说明|备注|
|--|--|--|
|cache|缓存策略|-   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表来一条数据，系统会先查找Cache，如果没有找到，则去物理维表中查询。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查询都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。 |
|cacheSize|缓存大小|选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存更新时间间隔|默认缓存不超时，单位为毫秒。不同缓存策略下的功能如下： -   LRU：设置缓存失效的超时时长。
-   ALL：设置缓存加载的间隔时长，默认不重新加载。 |

