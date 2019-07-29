# 创建云数据库RDS版结果表 {#concept_62525_zh .concept}

本文为您介绍如何创建实时计算云数据库RDS（DRDS）版结果表以及云数据库RDS版和实时计算字段类型之间的映射关系。

**说明：** 实时计算暂不支持引用RDS for MySQL 8.0版本作为数据存储。

## 什么是云数据库RDS版 {#section_ll5_gwf_cgb .section}

阿里云关系型数据库（Relational Database Service）简称RDS，是一种稳定可靠、可弹性伸缩的在线数据库服务。RDS基于阿里云分布式文件系统和高性能存储，支持MySQL、SQL Server、PostgreSQL和PPAS（Postgre Plus Advanced Server）引擎，并且提供了容灾、备份、恢复、监控、迁移等方面的全套解决方案。

## DDL定义 {#section_zpq_cwf_cgb .section}

实时计算支持使用RDS/DRDS作为结果输出（目前仅支持MySQL数据存储类型）。示例代码如下。

``` {#codeblock_aoz_tz2_1zo .language-sql}
CREATE TABLE rds_output(
 id INT,
 len INT,
 content VARCHAR,
 PRIMARY KEY (id,len)
) WITH (
 type='rds',
 url='<yourDatabaseURL>',
 tableName='<yourDatabaseTable>',
 userName='<yourDatabaseUserName>',
 password='<yourDatabasePassword>'
);        
```

**说明：** 

-   实时计算写入RDS/DRDS数据库结果表原理：针对实时计算每行结果数据，拼接成一行SQL语句，输入至目标端数据库，进行执行。如果使用批量写，需要在URL后面加上参数 `?rewriteBatchedStatements=true`，以提高系统性能。
-   RDS MySQL数据库支持自增主键。如果需要让实时计算写入数据支持自增主键，在DDL中不声明该自增字段即可。例如，ID是自增字段，实时计算DDL不写出该自增字段，则数据库在一行数据写入过程中会自动填补相关的自增字段。
-   如果DRDS有分区表，拆分键必须在实时计算DDL里`primary key（）`中声明，否则拆分的表无法写入。
-   建议使用数据存储注册方式，参见[注册云数据库RDS版](cn.zh-CN/Flink SQL开发指南/数据存储/注册数据存储/注册云数据库RDS版.md#)。

## WITH参数 {#section_l5t_1yf_cgb .section}

|参数|说明|备注|
|--|--|--|
|url|jdbc连接地址|url的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。内网地址参见如下链接： -   [RDS的内网地址](https://help.aliyun.com/document_detail/26128.html?spm=5176.doc43185.6.581.rxQuNz)
-   [DRDS的内网地址](https://help.aliyun.com/document_detail/56494.html)

 **说明：** 若访问通过VPC访问授权过的RDS，对应的url配置请参见[VPC访问授权](cn.zh-CN/Flink SQL开发指南/数据存储/VPC访问授权.md#)。

 |
|tableName|表名|无。|
|userName|用户名|无。|
|password|密码|无。|
|maxRetryTimes|最大重试次数|可选，默认值为3。|
|batchSize|一次批量写入的条数|可选，默认值为50。|
|bufferSize|流入多少条数据后开始去重|可选，需要指定主键才生效。默认值为1000，表示输入的数据达到1000条即开始输出。|
|flushIntervalMs|清空缓存的时间间隔|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|excludeUpdateColumns|忽略指定字段的更新|可选，默认值为空（默认忽略primary key字段）。表示更新主键值相同的数据时，忽略指定字段的更新。|
|ignoreDelete|是否忽略delete操作|可选，默认为false，表示支持delete功能。|
|partitionBy|分区|可选，默认为空。表示写入Sink节点前，会根据该值做hash。数据会流向相应的hash节点。|

## 类型映射 {#section_edk_byf_cgb .section}

|RDS字段类型|实时计算字段类型|
|-------|--------|
|INTEGER|INT|
|FLOAT|FLOAT|
|DECIMAL|DECIAML|
|LONG|BIGINT|
|DOUBLE|DOUBLE|
|TEXT|VARCHAR|
|BYTE|
|DATE|
|DATETIME|
|TIMESTAMP|
|TIME|
|YEAR|
|CHAR|

## JDBC连接参数 {#section_drk_cyf_cgb .section}

|参数名称|参数说明|默认值|最低版本要求|
|----|----|---|------|
|useUnicode|是否使用Unicode字符集，如果参数characterEncoding设置为gb2312或gbk，本参数值必须设置为true。|false|1.1g|
|characterEncoding|当useUnicode设置为true时，指定字符编码。比如可设置为gb2312或gbk。|false|1.1g|
|autoReconnect|当数据库连接异常中断时，是否自动重新连接。|false|1.1|
|autoReconnectForPools|是否使用针对数据库连接池的重连策略。|false|3.1.3|
|failOverReadOnly|自动重连成功后，连接是否设置为只读。|true|3.0.12|
|maxReconnects|autoReconnect设置为true时，重试连接的次数。|3|1.1|
|initialTimeout|autoReconnect设置为true时，两次重连之间的时间间隔，单位为秒。|2|1.1|
|connectTimeout|和数据库服务器建立socket连接时的连接超时时长，单位为毫秒。 0表示永不超时，适用于JDK 1.4及更高版本。|0|3.0.1|
|socketTimeout|socket操作（读写）超时，单位：毫秒。 0表示永不超时。|0|3.0.1|

## FAQ {#section_hrb_dyf_cgb .section}

-   Q：实时计算的结果数据写入RDS表，是按主键更新的，还是生成1条新的记录？

    A：如果在DDL中定义了主键，会采用`insert into on duplicate key update`的方式更新记录，也就意味着对于不存在的主键字段会直接插入，存在的主键字段则更新相应的值。 如果DDL中没有声明primary key，则会用`insert into` 方式插入记录，追加数据。

-   Q：使用RDS表中的唯一索引做group by需要注意什么？

    A：

    -   需要在作业中的primary key中声明这个唯一索引。
    -   RDS中只有一个自增主键，实时计算作业中不能声明为primary key。

