# 创建云数据库Redis版结果表

本文为您介绍如何创建实时计算Flink版云数据库Redis版结果表以及创建过程中涉及的WITH参数、类型映射和属性字段。

**说明：**

-   本文仅适用于Blink 3.2.0及以上版本。
-   实时计算Flink版Redis结果表支持自建Redis服务。

## 什么是云数据库Redis版

阿里云数据库Redis版是兼容开源Redis协议标准、提供内存加硬盘混合存储的数据库服务，基于高可靠双机热备架构及可平滑扩展的集群架构，充分满足高吞吐、低延迟及弹性变配的业务需求。实时计算Flink版支持将云数据库Redis版作为流式数据的输出。

## 语法示例

云数据库Redis版结果表支持5种Redis数据结构，其DDL定义如下：

-   STRING类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`set key value`。

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'string',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认值为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   LIST类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`lpush key value`。

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'list',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认值为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   SET类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`sadd key value`。

    ```
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'set',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认值为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   HASHMAP类型

    DDL为三列：第1列为key，第2列为hash\_key，第3列为hash\_key对应的hash\_value。Redis插入数据的命令为`hmset key hash_key hash_value`。

    ```
    create table resik_output (
      a varchar,
      b varchar, 
      c varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'hashmap',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认值为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   SORTEDSET类型

    DDL为三列：第1列为key，第2列为score，第3列为value。Redis插入数据的命令为`add key score value`。

    ```
    create table resik_output (
      a varchar,
      b double,  --必须为DOUBLE类型。
      c varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'sortedset',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认值为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```


## WITH参数

|参数|参数说明|是否必填|取值|
|--|----|----|--|
|type|结果表类型|是|固定值为`redis`。|
|mode|对应Redis的数据结构|取值如下： -   string
-   list
-   set
-   hashmap
-   sortedset |
|host|Redis Server对应地址|取值示例：`127.0.0.1`。|
|port|Redis Server对应端口|否|默认值为6379。|
|dbNum|Redis Server对应数据库序号|默认值为0。|
|ignoreDelete|是否忽略Retraction消息|默认值为false，可取值为true或false。如果设置为false，收到Retraction时，同时删除数据对应的key及已插入的数据。|
|password|Redis Server对应的密码|默认值为空，不进行权限验证。|

## 类型映射

Redis和实时计算Flink版字段类型对应关系如下。建议您使用该对应关系进行DDL声明。

|Redis字段类型|实时计算Flink版字段类型|
|---------|--------------|
|STRING|VARCHAR|
|SCORE|DOUBLE|

**说明：** 因为Redis的SCORE类型应用于SORTEDSET（有序集合），所以需要手动为每个Value设置一个DOUBLE类型的SCORE，Value才能按照该SCORE从小到大进行排序。

## 代码示例

包含Redis结果表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE random_stream (
  v VARCHAR, 
  p VARCHAR) with (
    type = 'random'
);

create table resik_output (
  a VARCHAR,
  b VARCHAR,
  primary key(a) 
) with (
  type = 'redis',
  mode = 'string',
  host = '<yourRedisHost>',
  password = '<yourRedisPassword>'
);

INSERT INTO resik_output 
SELECT v, p
FROM random_stream;
```

