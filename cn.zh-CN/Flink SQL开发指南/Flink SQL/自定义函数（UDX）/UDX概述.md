# UDX概述 {#concept_69463_zh .concept}

本文为您介绍如何为实时计算自定义函数搭建环境和使用。

**说明：** 仅独享模式支持自定义函数。

## UDX分类 {#section_190_h60_4it .section}

实时计算支持以下3类自定义函数：

|UDX分类|描述|
|-----|--|
|UDF（User Defined Function）|用户自定义标量值函数（User Defined Scalar Function）。其输入与输出是一对一的关系，即读入一行数据，写出一条输出值。|
|UDAF（User Defined Aggregation Function）|自定义聚合函数，其输入与输出是多对一的关系， 即将多条输入记录聚合成一条输出值。可以与SQL中的GROUP BY语句联用。具体语法请参见[聚合函数](../../../../cn.zh-CN/开发/SQL及函数/内建函数/聚合函数.md#)。|
|UDTF（User Defined Table-valued Function）|自定义表值函数，调用一次函数输出多行或多列数据。|

## UDX DEMO {#section_1ou_984_z4n .section}

阿里云团队为您提供UDX DEMO便于您快速进行业务开发。以下实时计算UDX DEMO中包含UDF、UDAF和UDTF的实现，供您参考：

**说明：** 

-   DEMO示例中已为您配置对应版本的开发环境，您无需进行环境搭建。
-   DEMO为Maven项目，您可使用IntelliJ IDEA进行开发。开发方法请参见[使用IntelliJ IDEA开发自定义函数](cn.zh-CN/Flink SQL开发指南/Flink SQL/自定义函数（UDX）/使用IntelliJ IDEA开发自定义函数.md#)。

-   实时计算3.0版本

    [blink\_udx\_3x](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1565689446746/blink_customersink_3x.tar.gz)

-   实时计算2.0版本

    [blink\_udx\_2x](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1565689659066/blink_customersink_2x.tar.gz)

-   实时计算1.0版本

    [blink\_udx\_1x](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1565683878129/blink_udx_1x.tar.gz)


## 环境搭建 {#section_ck2_gcm_cgb .section}

以上DEMO主要使用的依赖JAR包如下，如您需要单独使用，可自行下载：

-   基于实时计算3.2.1以下版本：
    -   [flink-streaming-java\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327398632/flink-streaming-java_2.11-blink-2.2.4.jar)
    -   [flink-table\_2.11](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543327437386/flink-table_2.11-blink-2.2.4.jar)
    -   [flink-core-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/98378/cn_zh/1543326995841/flink-core-blink-2.2.4.jar)
-   基于实时计算3.2.1及以上版本：

    请根据需求自行添加开源版本所支持的[POM依赖包](https://search.maven.org/search?q=com.alibaba.blink)。 下载查看[完整依赖包示例](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/111995/cn_zh/1553501574644/pom.xml)。

    **说明：** 如果您需要依赖Snapshot版本，可以自行添加Snapshot版本所支持的[POM依赖包](https://oss.sonatype.org/content/repositories/snapshots/com/alibaba/blink/flink-core/)。


## 注册使用 {#section_5q4_9pi_lny .section}

1.  [登录实时计算控制台](https://stream.console.aliyun.com)。
2.  单击顶部菜单中的开发，进入开发页面。
3.  在左侧的导航栏中单击**资源引用**。
4.  在**资源引用**页签的右上角，单击**新建资源**。
5.  在上传资源页面输入资源配置信息。

    |参数名称|说明|
    |----|--|
    |上传方式|当前仅支持本地上传。|
    |资源选择|单击**选择资源**，选择需要引用的资源。|
    |资源名称|输入资源名称。|
    |资源备注|输入资源备注信息。|
    |资源类型|选择引用资源类型，JAR、DICTIONARY或PYTHON。|

6.  在**资源引用**页签中，将鼠标悬停在对应作业的右侧的**更多**上。
7.  选择下拉列表中的**引用**，将资源中的代码输入实时计算作业编辑窗口。
8.  在作业的编辑窗口的顶部，输入自定义函数声明，示例如下。

    ``` {#codeblock_jpv_uww_av5 .language-SQL}
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


## 参数与返回值类型 {#section_584_qiv_6ll .section}

实时计算支持定义Java UDX时，使用Java类型作为参数和返回值。下面为实时计算类型和Java类型的映射关系。

|实时计算数类型|Java类型|
|-------|------|
|TINYINT|java.lang.Byte|
|SAMLLINT|java.lang.Short|
|INT|java.lang.Integer|
|BIGINT|java.lang.Long|
|FLOAT|java.lang.Float|
|DOUBLE|java.lang.Double|
|DECIMAL|java.math.BigDecimal|
|BOOLEAN|java.lang.Boolean|
|DATE|java.sql.Date|
|TIME|java.sql.Time|
|TIMESTAMP|java.sql.Timestamp|
|CHAR|java.lang.Character|
|STRING|java.lang.String|
|VARBINARY|java.lang.byte\[\]|
|ARRAY|暂不支持|
|MAP|暂不支持|

## 自定义函数参数获取 {#section_zjd_fbn_jhb .section}

在`UDX`中提供了可选的`open(FunctionContext context)` 方法，`FunctionContext`具备参数传递功能，自定义配置项可以通过此对象来传递。

假设需要在作业中添加如下2个参数。

``` {#codeblock_xmy_32z_36o .language-java}
testKey1=lincoln
test.key2=todd
```

以UDTF为例，在open方法中通过`context.getJobParameter`即可获取。示例如下。

``` {#codeblock_8mf_er1_urb .language-java}
public void open(FunctionContext context) throws Exception {
      String key1 = context.getJobParameter("testKey1", "empty");
      String key2 = context.getJobParameter("test.key2", "empty");
      System.err.println(String.format("end open: key1:%s, key2:%s", key1, key2));
}
```

**说明：** 具体的作业参数可参见[作业参数](cn.zh-CN/Flink SQL开发指南/配置调优/手动配置调优.md#ul_r1g_lhm_bgb)。

