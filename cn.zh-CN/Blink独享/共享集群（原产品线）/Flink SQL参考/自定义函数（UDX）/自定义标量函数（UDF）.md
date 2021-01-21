# 自定义标量函数（UDF）

本文为您介绍如何为实时计算Flink版自定义标量函数（UDF）搭建开发环境、编写业务代码及上线。

**说明：** 阿里云实时计算Flink版共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 定义

自定义标量函数（UDF）将0个、1个或多个标量值映射到一个新的标量值。

## 搭建环境

搭建开发环境，请参见[环境搭建](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/自定义函数（UDX）/概述.md)。

## 业务代码

UDF需要在ScalarFunction类中实现`eval`方法。`open`方法和`close`方法可选。

**说明：** UDF默认对于相同的输入会有相同的输出。如果UDF不能保证相同的输出，例如，在UDF中调用外部服务，相同的输入值可能返回不同的结果，建议您使用`override isDeterministic()`方法，返回`False`。否则在某些条件下，输出结果不符合预期。例如，UDF算子前移。

以Java为例，示例代码如下。

```
package com.hjc.test.blink.sql.udx;

import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.ScalarFunction;

public class StringLengthUdf extends ScalarFunction {
    // 可选，open方法可以不写。
    // 如果编写open方法需要声明'import org.apache.flink.table.functions.FunctionContext;'。
    @Override
    public void open(FunctionContext context) {
        }
    public long eval(String a) {
        return a == null ? 0 : a.length();
    }
    public long eval(String b, String c) {
        return eval(b) + eval(c);
    }
    //可选，close方法可以不写。
    @Override
    public void close() {
        }
}
```

## 编写SQL语句

在指定的Class中编写SQL语句，自定义函数中SQL语句示例如下。

```
-- udf str.length()
CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';

create table sls_stream(
    a int,
    b int,
    c varchar
) with (
    type='sls',
    endPoint='<yourEndpoint>',
    accessKeyId='<yourAccessId>',
    accessKeySecret='<yourAccessSecret>',
    startTime = '2017-07-04 00:00:00',
    project='<yourProjectName>',
    logStore='<yourLogStoreName>',
    consumerGroup='consumerGroupTest1'
);

create table rds_output(
    id int,
    len bigint,
    content VARCHAR
) with (
    type='rds',
    url='yourDatabaseURL',
    tableName='<yourDatabaseTableName>',
    userName='<yourDatabaseUserName>',
    password='<yourDatabasePassword>'
);

insert into rds_output
select
    a,
    stringLengthUdf(c),
    c as content
from sls_stream;
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

在作业**开发**页面单击**上线**后，在**运维**页面单击**启动**，即可完成自定义函数的上线。

## 常见问题

Q：为什么实现了一个随机数生成函数，在运行时产生的值却一样？

A：如果自定义函数是无参的，并且没有声明是非确定性函数，编译期间可能会被优化成一个常量值。如果需要让函数变成非确定性函数，不被优化为常量，建议您使用`override isDeterministic()`方法，返回`False`。

