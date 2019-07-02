# 创建Redis结果表 {#concept_263088 .concept}

本文为您介绍如何创建实时计算Redis结果表。

**说明：** 本文档仅支持实时计算3.0及以上版本。

## DDL定义 {#section_kxa_zmy_pgd .section}

目前Redis结果表支持5种Redis数据结构，其DDL定义如下：

-   STRING类型

    DDL为2列：第1列为key；第2列为value。Redis插入数据的命令为`set key value`。

    ``` {#codeblock_5k3_olh_1dj}
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'string',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除之前插入的数据，默认为false。
    )
    ```

-   LIST类型

    DDL为2列：第1列为key；第2列为value。Redis插入数据的命令为`lpush key value`。

    ``` {#codeblock_7va_8yo_0qh}
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'list',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除之前插入的数据，默认为false。
    )
    ```

-   SET类型

    DDL为2列：第1列为key；第2列为value。Redis插入数据的命令为`sadd key value`。

    ``` {#codeblock_s3n_z3t_cvt}
    create table resik_output (
      a varchar,
      b varchar,
      primary key(a)
    ) with (
      type = 'redis',
      mode = 'set',
      host = '${redisHost}', -- 例如，'127.0.0.1'。
      port = '${redisPort}', -- 例如，'6379'。
      dbNum = '${dbNum}', -- 默认为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除之前插入的数据，默认为false。
    )
    ```

-   HASHMAP类型

    DDL为3列：第1列为key；第2列为hash\_key；第3列为hash\_key对应的hash\_value。Redis插入数据的命令为`hmset key hash_key hash_value`。

    ``` {#codeblock_vou_p6p_sf5}
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
      dbNum = '${dbNum}', -- 默认为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除之前插入的数据，默认为false。
    )
    ```

-   SORTEDSET类型

    DDL为3列：第1列为key；第2列为score；第3列为value。Redis插入数据的命令为`add key score value`。

    ``` {#codeblock_jh9_u0f_uwg}
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
      dbNum = '${dbNum}', -- 默认为0。
      ignoreDelete = 'true' -- 收到Retraction时，是否删除之前插入的数据，默认为false。
    )
    ```


## WITH参数 {#section_7ws_ush_l8m .section}

|参数|参数说明|是否必选|取值|
|--|----|----|--|
|type|结果表类型|是|redis|
|mode|对应Redis的数据结构|可取以下值： -   stirng
-   list
-   set
-   hashmap
-   sortedset

 |
|host|Redis server对应地址|取值示例：`127.0.0.1`|
|port|Redis server对应端口|否|默认值为6379|
|dbNum|Redis server对应数据库序号|默认值为0|
|ignoreDelete|是否忽略Retraction消息|默认值为false，可取值为true或false。如果设置为true，收到Retraction时，同时删除数据对应的key及之前插入的数据。|
|password|Redis Server 对应的密码|用户配置参数|

