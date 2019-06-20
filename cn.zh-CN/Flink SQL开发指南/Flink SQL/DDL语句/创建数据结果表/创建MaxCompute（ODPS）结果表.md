# 创建MaxCompute（ODPS）结果表 {#concept_pql_cdz_lgb .concept}

本文为您介绍如何创建MaxCompute（ODPS）结果表以及创建过程中的常见问题。

## DDL定义 {#section_jnb_3qf_cgb .section}

实时计算支持使用ODPS作为结果输出，示例代码如下。

``` {#codeblock_jau_xv8_4zk .language-sql}
create table odps_output(
    id INT,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'odps',
    endPoint = 'http://service.cn.maxcompute.aliyun-inc.com/api',
    project = '<projectName>',
    tableName = '<tableName>',
    accessId = '<yourAccessKeyId>',
    accessKey = '<yourAccessKeySecret>',
    `partition` = 'ds=2018****'
);
```

## WITH参数 {#section_glb_mqf_cgb .section}

|参数|说明|备注|
|--|--|--|
|endPoint|ODPS服务地址|必选，参见[MaxCompute开通Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|可选，参见[MaxCompute开通Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。 **说明：** VPC环境下为必填。

 |
|project|ODPS项目名称|必选|
|tableName|表名|必选|
|accessId|accessId|必选|
|accessKey|accessKey|必选|
|partition|分区名|可选，如果存在分区表则必填。具体分区信息可在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)查看。例如: 一个表的分区信息为`ds=20180905`，则可以写 ``partition` = 'ds=20180905'`。多级分区之间用逗号分隔，\*``partition` = 'ds=20180912,dt=xxxyyy'`。 **说明：** 如果分区的值不做填写，将会根据数据源的值写入不同的分区，即动态分区。（仅实时计算3.2.1及以上版本支持。）例如，\`partition\`='ds'，即根据ds字段的数据源的值写入不同分区。 对于动态分区字段为空的情况，如果数据源中ds=null或者ds=''，则输出结果为：

-   3.2.1及以前的版本会抛出空指针异常（NPE）。
-   3.2.2及以后的版本会创建ds=NULL的分区。

 |
|isOverwrite|写入Sink前，是否将结果表或者结果表的数据清空。| -   实时计算3.2以下版本默认参数值为true。
-   实时计算3.2及以上版本默认参数值为false。声明MaxCompute的流式作业结果表时，isOverwrite参数必须为false，否则在编译时会抛出异常。

 **说明：** 实时计算3.2及以上版本支持isOverwrite参数值变更。实时计算3.2以下版本如需变更isOverwrite参数值，请升级版本。

 |

## 常见问题 {#section_lsc_fgz_lgb .section}

1.  Q: Stream模式的MaxCompute结果表是否支持`isOverwrite`为`true`？

    A：实时计算3.2以下版本支持；实时计算3.2及以上版本不支持。

    `isOverwrite`为`true`，即写入结果表之前会把结果表或者结果数据清空。作业每次启动后和暂停恢复后、写入之前会把原来结果表或者结果分区里的内容删除。

    -   实时计算3.2以下版本isOverwrite默认值是true，且不支持修改。流式作业完成暂停/恢复操作后，会造成数据丢失。
    -   实时计算3.2及以上版本isOverwrite默认值是false，且声明MaxCompute流式作业结果表时isOverwrite参数值需要设置为false，否则在编译时会抛出异常。Stream模式MaxCompute结果表具备At Least Once数据保障机制，在作业运行失败后，可能会出现数据重复。
2.  Q: MaxCompute中的数据类型和实时计算中数据类型的对应关系是什么？

    A：如下表。

    |MaxCompute|Blink|
    |----------|-----|
    |TINYINT|TINYINT|
    |SMALLINT|SMALLINT|
    |INT|INT|
    |BIGINT|BIGINT|
    |FLOAT|FLOAT|
    |DOUBLE|DOUBLE|
    |BOOLEAN|BOOLEAN|
    |DATETIME|TIMESTAMP|
    |TIMESTAMP|TIMESTAMP|
    |STRING|VARCHAR|
    |DECIMAL|DECIMAL|
    |BINARY|VARBINARY|

    **说明：** 

    -   其他MaxCompute类型，ODPS Connector暂时还不支持转换。
    -   VARCHAR是ODPS新增加的类型，Blink Connectors暂不支持，建议您将ODPS的Schema中的VARCHAR类型设置为STRING类型。

