# 自定义聚合函数（UDAF）

本文为您介绍如何为实时计算Flink版自定义聚合函数（UDAF）搭建开发环境、编写业务代码及上线。

**说明：** 阿里云实时计算Flink版共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 定义

自定义聚合函数（UDAF）可以将多条记录聚合成1条记录。

## UDAF抽象类内部方法

**说明：** 虽然UDAF可以使用Java或Scala实现，但是建议您使用Java，因为Scala的数据类型有时会造成不必要的性能损失。

AggregateFunction的核心接口方法，如下所示：

-   createAccumulator和getValue方法

    ```
    /*
    * @param <T> UDAF的输出结果的类型。
    * @param <ACC> UDAF的accumulator的类型。accumulator是UDAF计算中用来存放计算中间结果的数据类型。您可以需要根据需要自行设计每个UDAF的accumulator。
    */
    public abstract class AggregateFunction<T, ACC> extends UserDefinedFunction {
    /*
    * 初始化AggregateFunction的accumulator。
    * 系统在进行第一个aggregate计算之前，调用一次此方法。
    */
    public ACC createAccumulator()；
    /*
    * 系统在每次aggregate计算完成后，调用此方法。
    */
    public T getValue(ACC accumulator)；
    }
    ```

    **说明：**

    -   createAccumulator和getValue可以定义在AggregateFunction抽象类内。
    -   UDAF必须包含1个accumulate方法。
-   accumulate方法

    ```
    public void accumulate(ACC accumulator, ...[用户指定的输入参数]...);
    ```

    **说明：**

    -   您需要实现一个accumulate方法，来描述如何计算输入的数据，并更新数据到accumulator中。
    -   accumulate方法的第一个参数必须是使用AggregateFunction的ACC类型的accumulator。在系统运行过程中，runtime代码会把accumulator的历史状态和您指定的上游数据（支持任意数量，任意类型的数据）作为参数，一起传递给accumulate方法。
-   retract和merge方法

    createAccumulator、getValue和accumulate 3个方法一起使用，可以设计出一个最基本的UDAF。但是实时计算Flink版一些特殊的场景需要您提供retract和merge两个方法才能完成。

    通常，计算都是对无限流的一个提前的观测值（early firing）。既然有early firing，就会有对发出的结果的修改，这个操作叫作撤回（retract）。SQL翻译优化器会帮助您自动判断哪些情况下会产生撤回的数据，哪些操作需要处理带有撤回标记的数据。但是您需要实现一个retract方法来处理撤回的数据。

    ```
    public void retract(ACC accumulator, ...[您指定的输入参数]...);
    ```

    **说明：**

    -   retract方法是accumulate方法的逆操作。例如，实现Count功能的UDAF，在使用accumulate方法时，每来一条数据要加1；在使用retract方法时，就要减1。
    -   类似于accumulate方法，retract方法的第1个参数必须使用AggregateFunction的ACC类型的accumulator。在系统运行过程中，runtime代码会把accumulator的历史状态，和您指定的上游数据（任意数量，任意类型的数据）一起发送给retract计算。
    在实时计算Flink版中一些场景需要使用merge方法，例如session window。由于实时计算Flink版具有out of order的特性，后输入的数据有可能位于2个原本分开的session中间，这样就把2个session合为1个session。此时，需要使用merge方法把多个accumulator合为1个accumulator。

    ```
    public void merge(ACC accumulator, Iterable<ACC> its);
    ```

    **说明：**

    -   merge方法的第1个参数，必须是使用AggregateFunction的ACC类型的accumulator，而且第1个accumulator是merge方法完成之后，状态所存放的地方。
    -   merge方法的第2个参数是1个ACC类型的accumulator遍历迭代器，里面有可能存在1个或多个accumulator。

## 搭建开发环境

搭建开发环境请参见[环境搭建](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/概述.md)。

## 编写业务逻辑代码

以Java为例，举例代码如下。

```
import org.apache.flink.table.functions.AggregateFunction;

public class CountUdaf extends AggregateFunction<Long, CountUdaf.CountAccum> {
    //定义存放count UDAF状态的accumulator的数据的结构。
    public static class CountAccum {
        public long total;
    }

    //初始化count UDAF的accumulator。
    public CountAccum createAccumulator() {
        CountAccum acc = new CountAccum();
        acc.total = 0;
        return acc;
    }

    //getValue提供了如何通过存放状态的accumulator计算count UDAF的结果的方法。
    public Long getValue(CountAccum accumulator) {
        return accumulator.total;
    }

    //accumulate提供了如何根据输入的数据更新count UDAF存放状态的accumulator。
    public void accumulate(CountAccum accumulator, Object iValue) {
        accumulator.total++;
    }

    public void merge(CountAccum accumulator, Iterable<CountAccum> its) {
         for (CountAccum other : its) {
            accumulator.total += other.total;
         }
    }
}
```

**说明：** AggregateFunction的子类支持open和close方法作为可选方法，请参见[自定义标量函数（UDF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义标量函数（UDF）.md)或[自定义表值函数（UDTF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义表值函数（UDTF）.md)的写法。

## 注册使用

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  在顶部菜单中，单击**开发**。
3.  在左侧的导航栏中，单击**资源引用**。
4.  在**资源引用**页签的左上角，单击**新建资源**。
5.  在**上传资源**页面，输入资源配置信息。

    |参数名称|说明|
    |----|--|
    |上传方式|实时计算控制台上仅支持本地上传。**说明：** 本地上传JAR包的大小上限为300 MB。如果JAR包大小超过300 MB，请在集群绑定的OSS控制台上，或通过OpenAPI的方式上传JAR包。 |
    |资源选择|单击**选择资源**，选择需要引用的资源。|
    |资源名称|输入资源名称。|
    |资源备注|输入资源备注信息。|
    |资源类型|选择引用资源类型，JAR、DICTIONARY或PYTHON。|

6.  在**资源列表**中，将鼠标悬停在目标资源右侧的**更多**上。
7.  在下拉列表中，单击**引用**。
8.  在作业编辑窗口，输入自定义函数声明，示例如下。

    ```
    CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
    ```


## 上线和启动

自定义聚合函数（UDAF）的上线和启动步骤，请参见[上线](/intl.zh-CN/独享模式/Flink SQL开发指南/作业开发/上线.md)和[启动](/intl.zh-CN/独享模式/Flink SQL开发指南/作业开发/启动.md)。

## 示例

```
-- UDAF计算count
CREATE FUNCTION countUdaf AS 'com.hjc.test.blink.sql.udx.CountUdaf';

create table sls_stream(
   a int,
   b bigint,
   c varchar
) with (
   type='sls',
   endPoint='yourEndpoint',
   accessKeyId='yourAccessId',
   accessKeySecret='yourAccessSecret',
   startTime='2017-07-04 00:00:00',
   project='<yourPorjectName>',
   logStore='stream-test2',
   consumerGroup='consumerGroupTest3'
);

create table rds_output(
   len1 bigint,
   len2 bigint
) with (
   type='rds',
   url='yourDatabaseURL',
   tableName='<yourDatabaseTableName>',
   userName='<yourDatabaseUserName>',
   password='<yourDatabasePassword>'
);

insert into rds_output
select
   count(a),
   countUdaf(a)
from sls_stream;
```

