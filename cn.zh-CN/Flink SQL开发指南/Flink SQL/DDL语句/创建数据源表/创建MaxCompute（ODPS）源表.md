# 创建MaxCompute（ODPS）源表 {#concept_827390 .concept}

本文为您介绍如何创建MaxCompute（ODPS）源表。

## DDL定义 {#section_nfl_hkf_svd .section}

实时计算支持使用MaxCompute作为源表输入，示例代码如下。

``` {#codeblock_n03_3w3_5k8 .language-sql}
create table odps_source(
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

## WITH参数 {#section_bt7_gid_fxw .section}

|参数|说明|备注|
|--|--|--|
|endPoint|ODPS服务地址|必选，参见[MaxCompute开通Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|可选，参见[MaxCompute开通Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。 **说明：** VPC环境下为必填。

 |
|project|MaxCompute项目名称|必选。|
|tableName|MaxCompute表名|必选。|
|accessId|accessId|必选。|
|accessKey|accessKey|必选。|
|partition|分区名|可选，如果存在分区表则必填。具体分区信息可在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)查看。例如: 一个表的分区信息为`ds=20180905`，则可以写 ``partition` = 'ds=20180905'`。多级分区之间用逗号分隔，\*``partition` = 'ds=20180912,dt=xxxyyy'`。 **说明：** 如果不填写分区值，将会根据数据源的值写入不同的分区，即动态分区。（仅实时计算3.2.1及以上版本支持。）例如，\`partition\`='ds'，即根据ds字段的数据源的值写入不同分区。 如果动态分区字段为空，且数据源中ds=null或者ds=''，则输出结果为：

-   3.2.1及以前的版本会抛出空指针异常（NPE）。
-   3.2.2及以后的版本会创建ds=NULL的分区。

 |

## 常见问题 {#section_v49_t6j_k3o .section}

1.  Q：DDL中endpoint和tunnelEndpont参数配置错误的影响？

    A：point和tunnelEndpont参数介绍请参见[MaxCompute开通Region和服务连接对照表](../../../../cn.zh-CN/准备工作/配置Endpoint.md#section_f2d_51y_5db)。参数配置错误会导致以下问题：

    -   Endpoint配置错误：任务上线进度停滞在91%处。
    -   tunnelEndpoint配置错误：任务运行失败。
2.  Q：MaxCompute作为源表是否支持分区裁剪？

    A：支持分区裁剪。如果MaxCompute源表是分区表，而且对分区列具有筛选要求，即可筛选出MaxCompute对应分区的数据。例如， `SELECT a FROM odps_test where ds = '20180926'`，其中ds是分区列，即可筛选出`ds='20180926'`这个分区的数据。

3.  Q：提交任务阶段出现Akka超时报错，但是MaxCompute源表获取Metadata速率正常，应该怎样处理？

    A：请合并小文件，具体步骤请参见文档[MaxCompute小文件有关场景及解决方案](https://help.aliyun.com/knowledge_detail/84446.html)。

4.  Q: MaxCompute中的数据类型和实时计算中数据类型的对应关系是什么？

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
5.  Q：是否支持指定读取MaxCompute表所有分区的字典序最大的分区?

    A：实时计算3.2.1及以上版本支持该功能。在DDL的WITH参数中输入参数 ``partition` = 'ds=max_pt()'`即可，示例如下。

    ``` {#codeblock_xdv_ei0_97v .lanuage-SQL}
    with (
      type='odps',
      `partition` = 'ds=max_pt()',
      ...
    )
    ```

    **说明：** 

    -   请不要在`'ds=max_pt()'`等号的两侧添加空格，否则可能出现如下报错信息。

        ``` {#codeblock_a7r_66e_2ib .language-java}
        org.apache.flink.table.api.TableException: Partition specific format is invalid!
          at com.alibaba.blink.connectors.odps.util.PartitionConditionParser.validateAndParseMaxPartStr
        ```

    -   实时计算3.2.1以下版本如需此功能，请先升级至实时计算3.2.1及以上版本。

