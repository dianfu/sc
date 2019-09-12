# DDL概述 {#concept_62515_zh .concept}

本文为您介绍实时计算DDL语法以及在DDL使用过程中需要注意的字段映射和大小写敏感问题。

## 语法 {#section_stc_bhx_bgb .section}

``` {#codeblock_ds5_fjy_y6u .language-sql}
CREATE TABLE tableName
      (columnName dataType [, columnName dataType ]*)
      [ WITH (propertyName=propertyValue [, propertyName=propertyValue ]*) ];
```

## 说明 {#section_epj_2ao_qu9 .section}

阿里云实时计算本身不带有数据存储功能，所有涉及表创建DDL的操作，实际上均是对于外部数据表、存储的引用声明，如下所示。

``` {#codeblock_w1z_7pg_agr .language-sql}
CREATE TABLE mq_stream(
 a VARCHAR,
 b VARCHAR,
 c VARCHAR
) WITH (
 type='mq',
 topic='blink_mq_test',
 accessID='<yourAccessID>',
 accessKey='<yourAccessSecret>'
);
```

以上代码不是在Flink SQL中创建[消息队列（MQ）源表](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据源表/创建消息队列 MQ源表.md#)的`topic`，而是声明了一个名称为`mq_stream`的表引用。下游所有对MQ的Topic`blink_mq_test`的相关DML操作，均可以用别名`mq_stream`代替。

-   实时计算声明表的作用域是当前作业（1个SQL文件提交后生成1个实时计算作业），即上述有关`mq_stream`的声明仅在当前SQL有效。相同实时计算项目下不同SQL文件（作业）中同样可以声明名称为`mq_stream`的表。
-   按照SQL标准定义，DDL语法中关键字、表名、列名等不区分大小写。
-   表名、列名必须以字母或者数字开头，并且名称中只能包含字母、数字或下划线。
-   DDL声明不完全根据名称进行映射（取决于上游插件的性质）。建议您引用声明的字段名称、字段个数和外部表保持一致，避免因定义混乱而导致的数据错乱情况。

    **说明：** 对于声明表和外部表：

    -   上下游插件支持根据KEY取值，则不要求两者字段数量一致，但字段名称需要一致。
    -   上下游插件不支持根据KEY取值，则需要字段数量和字段顺序一致。

## 字段映射 {#section_lwh_s8s_nhn .section}

声明表的字段映射根据外部数据源是否有Shema，分为两大类别。

-   顺序映射

    适用于以MQ为代表的不带有Schema系统。这类系统通常是非结构化存储系统，不支持根据KEY取值。建议您在DDL SQL声明中对字段名称进行自定义，并且和外部表的字段类型、字段数量保持一致。

    如下以MQ的1条记录为例。

    ``` {#codeblock_y29_stl_0jg}
    asavfa,sddd32,sdfdsv                    
    ```

    按照命名规范设置MQ的字段名。

    ``` {#codeblock_4fi_vfw_tbu .language-sql}
    CREATE TABLE mq_stream(
     a VARCHAR,
     b VARCHAR,
     c VARCHAR
    ) WITH (
     type='mq',
     topic='blink_mq_test',
     accessID='<yourAccessID>',
     accessKey='<yourAccessSecret>'
    );
    ```

-   名称映射

    适用于带有Schema的系统。这类系统在表存储级别定义了字段名称以及字段类型，支持根据KEY取值。建议您在Flink SQL的声明中保持和外部数据存储Schema定义一致，包括字段名称、字段数量以及字段的顺序。

    **说明：** 如果外部数据存储的字段名称是大小写敏感类型（如[表格存储](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据结果表/创建表格存储 TableStore结果表.md#)），则需要在区分大小写的字段名称处使用反引号`｀｀`进行转换。在DDL语法中，声明表的字段名和外部表的字段名需要一致。

    DataHub定义的Schema如下：

    |字段名|类型|
    |---|--|
    |name|STRING|
    |age|BIGINT|
    |value|STRING|

    **说明：** DataHub中的STRING数据类型对应的是在实时计算中的VARCHAR类型。

    关于上述DataHub声明的DDL如下：

    ``` {#codeblock_wwk_f7n_seh .language-sql}
    create  table stream_result (
        `name` varchar,  
        age bigint,
        `value` varchar  
      ) with (
          type='datahub',
          endpoint='http://dh-cn-hangzhou.aliyuncs.com',
          accessID='<yourAccessID>',
          accessKey='<yourAccessSecret>'
          project='project',
          topic='topic'
      );                       
    ```

    **说明：** 建议您将所有列进行声明引用。声明引用时可以减少字段，不能新增字段。


## 处理大小写敏感 {#section_n3j_ckx_bgb .section}

SQL标准定义中，大小写是不敏感的。如下所示，2段语句的含义相同。

``` {#codeblock_0kd_d4e_f4w .language-sql}
create table stream_result (
    name varchar,
    value varchar
);           
```

``` {#codeblock_z46_37b_3fo .language-sql}
create table STREAM_RESULT (
    NAME varchar,
    VALUE varchar
);           
```

但实时计算引用的大量外部数据源中，存在要求大小写敏感的数据源。例如，表格存储（Table Store）对于大小写是敏感的。如果需要在Table Store定义大写`NAME`字段，我们应该如下定义。

``` {#codeblock_z2k_189_i1u .language-sql}
create  table STREAM_RESULT (
    `NAME` varchar,
    `VALUE` varchar
);          
```

在之后所有的DML操作中，对于这个字段的引用均需要添加反引号`｀｀`，如下所示。

``` {#codeblock_phw_hl9_nze .language-sql}
INSERT INTO tableA
SELECT
  `NAME`,
  `VALUE`
FROM
  tableB;
```

## 相关章节 {#section_yxf_f3g_chb .section}

请参见以下文档，了解如何创建实时计算数据源表、数据结果表和数据维表。

-   [数据源表概述](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据源表/数据源表概述.md#)
-   [数据结果表概述](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据结果表/数据结果表概述.md#)
-   [数据维表概述](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据维表/数据维表概述.md#)

