---
keyword: [INSERT INTO, Sink]
---

# INSERT INTO语句

本文为您介绍如何在一个作业中写入一个Sink。

## 语法示例

-   源表

    ```
    CREATE TABLE datagen_source (
      name VARCHAR,
      score BIGINT
    )
    COMMENT 'datagen source table' --创建datagen源表时，必填。
    WITH (
      'connector' = 'datagen'
    );
    ```

-   结果表

    ```
    create table blackhole_sink(
      name VARCHAR,
      score BIGINT
    )
    COMMENT 'blackhole sink table' --创建blackhole结果表时，必填。
    with (
      'connector' = 'blackhole'
    );
    ```

-   DML

    ```
    INSERT INTO blackhole_sink SELECT UPPER(name), score FROM datagen_source;
    ```


