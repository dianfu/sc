# 创建表格存储 TableStore维表 {#concept_62533_zh .concept}

本文为您介绍如何创建实时计算表格存储 TableStore维表。

## 什么是表格存储 TableStore {#section_wd3_vxm_cgb .section}

表格存储 TableStore简称OTS，是根据99.99%的高可用以及11个9的数据可靠性的设计标准，构建在阿里云飞天分布式系统之上的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发上的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## 示例 {#section_ysz_xxm_cgb .section}

实时计算支持表格存储 TableStore作为维表，示例如下。

``` {#codeblock_amn_tbr_8ef .language-sql}
CREATE TABLE ots_dim_table (
 id int,
 len int,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME--定义了维表的变化周期，即表明该表是一张会变化的表。
) WITH (
 type='ots',
 endPoint='<yourEndpoint>'
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>',
);
```

**说明：** 声明一个维表的时候，必须要指名主键。维表JOIN的时候，ON的条件必须包含所有主键的等值条件。OTS的主键即表的rowkey。

## WITH参数 {#section_nns_yxm_cgb .section}

|参数|注释说明|
|--|----|
|instanceName|实例名|
|tableName|表名|
|endPoint|实例访问地址|
|accessId|访问的ID|
|accessKey|访问的键|

## CACHE参数 {#section_tzp_zxm_cgb .section}

|参数|注释说明|备注|
|--|----|--|
|cache|缓存策略|默认为 `None`, 可选 `LRU`。|
|cacheSize|缓存大小|当选择 LRU缓存策略后，可以设置缓存大小，默认为10000 行。|
|cacheTTLMs|缓存超时时间，单位为毫秒。|当选择 LRU缓存策略后，可以设置缓存失效的超时时间。|

## 测试案例 {#section_frs_51n_cgb .section}

``` {#codeblock_nrc_bf4_xjt .language-SQL}
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

create table phoneNumber(
name VARCHAR,
phoneNumber bigint,
primary key(name),
PERIOD FOR SYSTEM_TIME--定义维表的变化周期
)with(
type='ots'
);

CREATE table result_infor(
id bigint,
phoneNumber bigint,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id
,w.phoneNumber
,t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --维表JOIN必须指定
ON t.name = w.name;
			
```

关于维表详细语法请参见[维表JOIN语句](cn.zh-CN/Flink SQL开发指南/Flink SQL/QUERY语句/维表JOIN语句.md#)。

