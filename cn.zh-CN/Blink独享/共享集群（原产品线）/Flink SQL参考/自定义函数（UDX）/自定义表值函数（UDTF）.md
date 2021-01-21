# 自定义表值函数（UDTF）

本文为您介绍如何为实时计算Flink版自定义表值函数（UDTF）搭建开发环境、编写业务代码以及上线。

**说明：** 阿里云实时计算Flink版共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 定义

与自定义的标量函数类似，自定义的表值函数（UDTF）将0个、1个或多个标量值作为输入参数（可以是变长参数）。与标量函数不同，表值函数可以返回任意数量的行作为输出，而不仅是1个值。返回的行可以由1个或多个列组成。

## 搭建开发环境

参见[环境搭建](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/自定义函数（UDX）/概述.md)。

## 编写业务逻辑代码

UDTF需要在TableFunction类中实现eval方法。open方法和close方法可选。以Java为例，示例代码如下。

```
package com.hjc.test.blink.sql.udx;

import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.TableFunction;

public class SplitUdtf extends TableFunction<String> {

    // 可选，open方法可不编写。如果编写，则需要添加声明'import org.apache.flink.table.functions.FunctionContext;'。
    @Override
    public void open(FunctionContext context) {
        // ... ...
        }

    public void eval(String str) {
        String[] split = str.split("\\|");
        for (String s : split) {
            collect(s);
        }
    }

    // 可选，close方法可不编写。
    @Override
    public void close() {
        // ... ...
        }
}
```

## 多行返回

UDTF可以通过多次调用`collect()`实现将1行的数据转为多行返回。

## 多列返回

UDTF不仅可以进行1行转多行，还可以1列转多列。如果您需要UDTF返回多列，只需要将返回值声明成Tuple或Row。Tuple或Row解释如下：

-   返回值为Tuple

    实时计算Flink版支持使用Tuple1到Tuple25 ，定义1个字段到25个字段。用Tuple3来返回3个字段的UDTF示例如下。

    ```
    import org.apache.flink.api.java.tuple.Tuple3;
    import org.apache.flink.table.functions.TableFunction;
    
    // 使用Tuple作为返回值，一定要显式声明Tuple的泛型类型, 例如，String、Long和Integer。
    public class ParseUdtf extends TableFunction<Tuple3<String, Long, Integer>> {
    
    public void eval(String str) {
    String[] split = str.split(",");
    // 以下代码仅作示例，实际业务需要添加更多的校验逻辑。
    String first = split[0];
    long second = Long.parseLong(split[1]);
    int third = Integer.parseInt(split[2]);
    Tuple3<String, Long, Integer> tuple3 = Tuple3.of(first, second, third);
    collect(tuple3);
    }
    }
    ```

    **说明：** 使用Tuple时，字段值不能为null，且最多只能存在25个字段。

-   返回值为Row

    使用Row来实现返回3个字段的UDTF示例如下。

    ```
    import org.apache.flink.table.types.DataType;
    import org.apache.flink.table.types.DataTypes;
    import org.apache.flink.table.functions.TableFunction;
    import org.apache.flink.types.Row;
    
    public class ParseUdtf extends TableFunction<Row> {
    
    public void eval(String str) {
    String[] split = str.split(",");
    String first = split[0];
    long second = Long.parseLong(split[1]);
    int third = Integer.parseInt(split[2]);
    Row row = new Row(3);
    row.setField(0, first);
    row.setField(1, second);
    row.setField(2, third);
    collect(row);
    }
    
    @Override
    // 如果返回值是Row，则必须重载实现getResultType方法，显式地声明返回的字段类型。
    public DataType getResultType(Object[] arguments, Class[] argTypes) {
    return DataTypes.createRowType(DataTypes.STRING, DataTypes.LONG, DataTypes.INT);
    }
    }
    ```

    **说明：** Row的字段值可以是null，但如果需要使用Row，必须重载实现`getResultType`方法。


## SQL语法

UDTF支持cross join和left join，在使用UDTF时需要添加`lateral`和`table`关键字。以`ParseUdtf`为例，需要先注册一个Function名字。

```
CREATE FUNCTION parseUdtf AS 'com.alibaba.blink.sql.udtf.ParseUdtf';
```

-   cross join

    左表的每一行数据都会关联上UDTF产出的每一行数据，如果UDTF不产出任何数据，则这1行不会输出。

    ```
    select S.id, S.content, T.a, T.b, T.c
    from input_stream as S,
    lateral table(parseUdtf(content)) as T(a, b, c);
    ```

-   left join

    左表的每一行数据都会关联上UDTF产出的每一行数据，如果UDTF不产出任何数据，则这1行的UDTF的字段会用null值填充。

    **说明：** left join UDTF语句后面必须添加`on true`参数。

    ```
    select S.id, S.content, T.a, T.b, T.c
    from input_stream as S
    left join lateral table(parseUdtf(content)) as T(a, b, c) on true;
    ```


## 注册使用

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  在顶部菜单中，单击**开发**。
3.  在左侧的导航栏中，单击**资源引用**。
4.  在**资源引用**页签的右上角，单击**新建资源**。
5.  在上传资源页面，输入资源配置信息。

    |参数名称|说明|
    |----|--|
    |上传方式|实时计算控制台上仅支持本地上传。**说明：** 本地上传JAR包的大小上限为300 MB。如果JAR包大小超过300 MB，请在集群绑定的OSS控制台上，或通过OpenAPI的方式上传JAR包。 |
    |资源选择|单击**选择资源**，选择需要引用的资源。|
    |资源名称|输入资源名称。|
    |资源备注|输入资源备注信息。|
    |资源类型|选择引用资源类型，JAR、DICTIONARY或PYTHON。|

6.  在**资源引用**页签中，将鼠标悬停在对应作业的右侧的**更多**上。
7.  在下拉列表中，选择**引用**。
8.  在作业的编辑窗口的顶部，输入自定义函数声明，示例如下。

    ```
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


## 上线和启动

自定义聚合函数（UDAF）的上线和启动步骤，请参见[上线](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业开发/上线.md)和[启动](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业开发/启动.md)。

## UDTF示例

```
-- UDTF str.split("\\|");
create function splitUdtf as 'com.hjc.test.blink.sql.udx.SplitUdtf';

create table sls_stream(
a INT,
b BIGINT,
c VARCHAR
) with (
type='sls',
endPoint='yourEndpoint',
accessKeyId='yourAccessKeyId',
accessKeySecret='yourAccessSecret',
startTime = '2017-07-04 00:00:00',
project='yourProjectName',
logStore='yourLogStoreName',
consumerGroup='consumerGroupTest2'
);

-- 将c字段传入splitUdtf，切分后得到多行1列的表T(s)。s表示字段名字。
create view v1 as
select a,b,c,s
from sls_stream,
lateral table(splitUdtf(c)) as T(s);

create table rds_output(
id INT,
len BIGINT,
content VARCHAR
) with (
type='rds',
url='yourDatabaseURL',
tableName='yourDatabaseTableName',
userName='yourDatabaseUserName',
password='yourDatabasePassword'
);

insert into rds_output
select
a,b,s
from v1;
```

