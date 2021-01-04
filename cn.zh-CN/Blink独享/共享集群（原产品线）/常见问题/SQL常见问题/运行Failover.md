---
keyword: [大小写敏感, AutoScale, Body, 字节编码, 链接超时, 处理数据的速率, 系统时间, 启动位点, 链接池, 解析, Taskmanager, Schema定义, CU不足, 主键, 空格, 内存溢出, 内存不足, state\_size, Native Memory, STS Token, 死锁, Emoji, java.long.ArrayIndexOutOfBoundsException -1, java.util.concurrent.TimeoutException, the message body size over max value, Caused by: org.codehaus.janino.JaninoRuntimeException: Code of method ... ... 64KB, Caused by: java.net.SocketException: Connection reset, Seek out of range, pool.GetConnectionTimeoutException, the last packet sent successfully to the server was milliseconds ago, Incorrect String value:'\\xF0\\x9F...'for column, Some executions status are not reported and failing the job now, The heartbeat of TaskManager with id container......timed out, cause by:java.lang.IndexOutOfBoundsException, No pooled slot available and request to ResourceManager for new slot failed, Ots attribute key type error, table ots\_sink column key uv, OutOfMemory, OutOfMemoryError: Java heap space, java.lang.StackOverflowError, Suspend the old job to update it, MySQLSyntaxErrorException: Unknown column, TaskManager lost/killed, The sts token has been expired, OTSParameterInvalid, Message: Invalid update row request: missing cells in request., There is an invalid character in tagvalue, 作业启动一段时间后报错停止，Failover里出现报错信息，应该如何处理？, 写MySQL（TDDL/RDS）时，出现死锁（DeadLock）, Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction]
---

# 运行Failover

本文汇总了实时计算Flink版运行Failover常见报错与解决方案。

-   [报错：java.long.ArrayIndexOutOfBoundsException -1](#section_wqs_3wt_p22)
-   [报错：java.util.concurrent.TimeoutException](#section_p1w_st4_icp)
-   [报错：the message body size over max value](#section_t4j_s0h_dee)
-   [报错：Caused by: org.codehaus.janino.JaninoRuntimeException: Code of method...64KB](#section_1az_hez_uqt)
-   [报错：Caused by: java.net.SocketException: Connection reset](#section_f9p_3fq_iqi)
-   [报错：Seek out of range](#section_vrz_dtp_cs1)
-   [报错：pool.GetConnectionTimeoutException](#section_3sy_pwd_1l8)
-   [报错：the last packet sent successfully to the server was milliseconds ago](#section_lz5_66q_603)
-   [报错：Incorrect String value:'\\xF0\\x9F...'for column](#section_vmd_u8v_jrc)
-   [报错：Some executions status are not reported and failing the job now.](#section_8d9_laa_x9n)
-   [报错：The heartbeat of TaskManager with id container...timed out](#section_tvp_sku_que)
-   [报错：cause by:java.lang.IndexOutOfBoundsException](#section_hrd_1zb_95k)
-   [报错：No pooled slot available and request to ResourceManager for new slot failed](#section_w19_4iw_z3g)
-   [报错：Ots attribute key type error, table ots\_sink column key uv](#section_c77_ith_vvp)
-   [报错：OutOfMemory](#section_fco_yuj_z9s)
-   [报错：OutOfMemoryError: Java heap space](#section_wuj_uz0_o2j)
-   [报错：java.lang.StackOverflowError](#section_jir_4ct_a86)
-   [报错：Suspend the old job to update it](#section_hh4_wqw_282)
-   [报错：MySQLSyntaxErrorException: Unknown column](#section_lkw_p1v_1t3)
-   [报错：TaskManager lost/killed](#section_ku3_2g5_bo2)
-   [报错：The sts token has been expired](#section_lh4_3oq_a3r)
-   [报错：OTSParameterInvalid, Message: Invalid update row request: missing cells in request.](#section_lue_fzg_7o6)
-   [报错：There is an invalid character in tagvalue](#section_ioq_5du_mmc)
-   [报错：Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction](#section_vxa_dlo_11h)
-   [报错：Value '0000-00-00 00:00:00' can not be represented as java.sql.Timestamp](#section_w3r_aye_9vj)
-   [报错：Failover:offset \(0\) + length \(8\) exceed the capacity of the array: 1](#section_j1z_359_ad8)
-   [作业启动一段时间后报错停止，Failover里出现报错信息，应该如何处理？](#section_ovc_1lu_502)

## 报错：java.long.ArrayIndexOutOfBoundsException -1

-   报错信息

    ![ArrayIndexOutOfBoundsException](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p94818.png)

-   报错原因

    实时计算Flink版引用的外部数据源主键字段大小写敏感。

-   解决方案

    修改结果表的主键字段大小写，和外部数据源表保持一致。例如，如果分析型数据库MySQL版结果表的字段是大写的，则在实时计算Flink版的DDL语句中，结果表的主键字段也必须是大写的。


## 报错：java.util.concurrent.TimeoutException

-   报错信息

    AutoScale启动时，报错如下。

    ```
    java.util.concurrent.TimeoutException
      at org.apache.flink.runtime.concurrent.FutureUtils$Timeout.run(FutureUtils.java:834)
      at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
      at java.util.concurrent.FutureTask.run(FutureTask.java:266)
      at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:186)
      at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:299)
      at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1147)
      at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:622)
      at java.lang.Thread.run(Thread.java:834)   
    ```

-   报错原因

    作业上线时指定的CU数，不足以支持AutoScale进行迭代。

-   解决方案

    在作业开启上线时，如果开启AutoScale，则提高到**最大CU数**。详情请参见[AutoScale自动配置调试](https://help.aliyun.com/document_detail/111870.html)。


## 报错：the message body size over max value

-   报错信息

    ![报错信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p84988.png)

-   错误原因

    在数据写入MQ结果表时，MQ消息超过了Body的长度限制。详情请参见[Body长度限制]()。

-   解决方案

    调整MQ消息写入Body的长度，限制在Body长度范围内。


## 报错：Caused by: org.codehaus.janino.JaninoRuntimeException: Code of method...64KB

-   报错信息

    ```
    Caused by: java.lang.RuntimeException: Compiling "StreamExecCalcRule$6109": Code of method "calcProjectApply_1$(LStreamExecCalcRule$6109;Lorg/apache/flink/table/dataformat/BaseRow;)V" of class "StreamExecCalcRule$6109" grows beyond 64 KB
      at org.codehaus.janino.UnitCompiler.compileUnit(UnitCompiler.java:361)
      at org.codehaus.janino.SimpleCompiler.cook(SimpleCompiler.java:234)
      at org.codehaus.janino.SimpleCompiler.compileToClassLoader(SimpleCompiler.java:446)
      at org.codehaus.janino.SimpleCompiler.cook(SimpleCompiler.java:213)
      at org.codehaus.janino.SimpleCompiler.cook(SimpleCompiler.java:204)
      at org.codehaus.commons.compiler.Cookable.cook(Cookable.java:80)
      at org.codehaus.commons.compiler.Cookable.cook(Cookable.java:75)
      at org.apache.flink.table.codegen.CodeGenUtils$.compile(CodeGenUtils.scala:997)
      ... 18 more
    Caused by: org.codehaus.janino.JaninoRuntimeException: Code of method "calcProjectApply_1$(LStreamExecCalcRule$6109;Lorg/apache/flink/table/dataformat/BaseRow;)V" of class "StreamExecCalcRule$6109" grows beyond 64 KB
      at org.codehaus.janino.CodeContext.makeSpace(CodeContext.java:974)
      at org.codehaus.janino.CodeContext.write(CodeContext.java:884)
      ...           
    ```

-   报错原因

    JVM对某个方法的字节编码长度限制为64KB。而此作业中，单个字段的字节编码长度就已超过了64KB。

-   解决方案

    使用[自定义标量函数（UDF）](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/自定义函数（UDX）/自定义标量函数（UDF）.md)方法修改此作业。


## 报错：Caused by: java.net.SocketException: Connection reset

-   报错信息

    ```
    The last packet successfully received from the server was 3,991 milliseconds ago.  The last packet sent successfully to the server was 3,992 milliseconds ago.
      at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
      at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
      at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
      at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
      at com.mysql.jdbc.Util.handleNewInstance(Util.java:425)
      at com.mysql.jdbc.SQLError.createCommunicationsException(SQLError.java:989)
      at com.mysql.jdbc.MysqlIO.send(MysqlIO.java:3743)
      at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:2506)
      at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2677)
      at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2545)
      at com.mysql.jdbc.ConnectionImpl.setAutoCommit(ConnectionImpl.java:4842)
      at com.alibaba.druid.pool.DruidPooledConnection.setAutoCommit(DruidPooledConnection.java:709)
      ... 6 more
    Caused by: java.net.SocketException: Connection reset
      at java.net.SocketOutputStream.socketWrite(SocketOutputStream.java:113)
      at java.net.SocketOutputStream.write(SocketOutputStream.java:153)
      at java.io.BufferedOutputStream.flushBuffer(BufferedOutputStream.java:82)
      at java.io.BufferedOutputStream.flush(BufferedOutputStream.java:140)
      at com.mysql.jdbc.MysqlIO.send(MysqlIO.java:3725)
      ... 11 more     
    ```

-   报错原因

    链接超时导致了Failover。

-   解决方案

    1.  检查RDS是否为高可用模式，如果不是，请切换至高可用模式。
    2.  With参数中的`URL`中设置`autoReconnect=true`，如下图所示。

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/110610/cn_zh/1552445752534/autoreconnect.png)

        如果有多个参数，请用and（&）符号进行连接。

        ![代码](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p133080.png)

    **说明：** 如果按照以上方案操作后，还是报错，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?accounttraceid=f7b76db740fa486baa4b63bd5848fbc1idrb)。


## 报错：Seek out of range

-   报错信息

    ![seek out of range](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p133081.png)

-   原因分析
    -   实时计算Flink版处理数据的速率小于数据进入DataHub的速率，会造成数据堆积，延时增加。当延时大于DataHub生命周期时，实时计算Flink版读取的数据已经过期，最终导致系统报错。
    -   您的本地设备系统时间大于DataHub所在机器的时间。实时计算Flink版作业启动位点默认时间为您本地设备的系统时间，如果启动位点在DataHub机器时间之后，则会导致`Seek out of Range`的报错。
-   解决方案
    -   对实时计算Flink版的性能进行调优，使实时计算Flink版处理数据的速率大于等于数据进入DataHub的速率。调优方法请参见：
        -   [高性能Flink SQL优化技巧](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/高性能Flink SQL优化技巧.md)
        -   [AutoConf自动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/AutoConf自动配置调优.md)
        -   [AutoScale自动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/AutoScale自动配置调优.md)
        -   [手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)
    -   调整用户本地设备系统时间，使其和DataHub所在机器时间保持一致。

## 报错：pool.GetConnectionTimeoutException

-   报错信息

    ![pool.GetConnectionTimeoutException](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p85122.png)

-   报错原因

    Pool的链接池最大值为30个，而您的链接数大于30，导致数据写入失败。

-   解决方案

    调大维表或流表JOIN节点的Core参数。例如，Core的默认值是0.1，可以调成0.25或1。详情请参见[手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)。


## 报错：the last packet sent successfully to the server was milliseconds ago

-   报错信息
    -   `the last packet successfully received from the server was milliseconds ago`
    -   `the last packet sent successfully to the server was milliseconds ago`
-   报错原因

    如果RDS的`net_read_timeout`和`net_write_timeout`参数设置过小，在网络条件较差或客户端处理Block耗时较长时，会导致RDS连接实时计算Flink版提示连接超时的报错。

    |参数|作用|
    |--|--|
    |`net_read_timeout`和`net_write_timeout`|服务器端等待客户端发送的网络包和发送给客户端网络包的超时时间，该参数仅对TCP/IP链接并且是Activity状态下的线程有效。|

-   解决方案

    调整`net_read_timeout`参数和`net_write_timeout`参数值，操作步骤如下：

    1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
    2.  在页面左上角，选择实例所在区域。
    3.  单击目标实例ID。
    4.  在左侧导航栏中单击**参数设置**。
    5.  在**可修改参数**页签，查找`net_read_timeout`和`net_write_timeout`。

        **说明：** 可以使用ctrl+f或command+f的组合键在页面中搜索您需要修改的参数。

    6.  单击`net_read_timeout`和`net_write_timeout`后的![编辑图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p85123.png)。
    7.  输入目标值，并单击**确定**。
    8.  单击**可修改参数**页签右上角的**提交参数**。
    9.  在弹出的对话框中，单击**确定**，完成参数修改。

## 报错：Incorrect String value:'\\xF0\\x9F...'for column

-   报错信息

    ![Incorrect String value](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9162749951/p85130.png)

-   错误原因

    数据中存在Emoji表情，数据库编码不能解析。

-   解决方案

    您需要在RDS控制台对应实例的**参数设置**中，修改character\_set\_server参数值为utf8mb4。此外，数据库和表的字符集也要同样设置为utf8mb4。

    **说明：**

    -   character\_set\_server参数修改后，需要**重启实例**才能生效。
    -   如果需要将\\xF0\\x9F...字符串存入数据库，则将反斜线（\\）替换为双反斜线（\\\\）。

## 报错：Some executions status are not reported and failing the job now.

-   报错信息

    `Some executions status are not reported and failing the job now.`

-   错误原因

    因为作业没有启动成功，所以没有记录启动时间。默认启动时间为UNIX起始时间`1970-01-01 08：00:00`。

-   解决方案

    请重新[上线](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业开发/上线.md)并[启动](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业开发/启动.md)作业。


## 报错：The heartbeat of TaskManager with id container...timed out

-   报错信息

    ![The heartbeat of TaskManager](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85440.png)

-   报错原因
    -   分布式物理机网络失联。
    -   Failover的节点对应Taskmanager的内存设置太小，垃圾回收机制会导致心跳超时。
-   解决方案
    -   如果此问题出现的不频繁，则不用关注，通常Failover后作业能正常恢复。
    -   调大Failover的节点对应Taskmanager的内存值。

## 报错：cause by:java.lang.IndexOutOfBoundsException

-   报错信息

    引用MaxCompute维表时，报错如下。

    ![ODPS维表Failover](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85441.png)

-   报错原因

    不支持Schema定义中写入分区列。

-   解决方案

    将分区列从DDL中删除，详情请参见[创建MaxCompute维表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据维表/创建MaxCompute维表.md)。


## 报错：No pooled slot available and request to ResourceManager for new slot failed

-   报错信息

    ![No pooled slot available](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85447.png)

-   错误原因

    作业指定的CU不足。

-   解决方案

    您需要进行[手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)。


## 报错：Ots attribute key type error, table ots\_sink column key uv

-   报错信息

    ```
    com.alibaba.blink.streaming.connectors.common.exception.BlinkRuntimeException: 
    ************
    ERR_ID:
         CON-04000038
    CAUSE:
         Ots attribute key type error, table ots_sink  column key  uv
    ACTION:
         contact ots admin for help.
    DETAIL:
    
    ************
        at com.alibaba.blink.streaming.connectors.common.exception.ErrorUtils.getException(ErrorUtils.java:39)
        at com.alibaba.blink.streaming.connectors.common.exception.ErrorUtils.throwException(ErrorUtils.java:53)
        at com.alibaba.blink.connectors.ots.OtsOutputFormat.createPut(OtsOutputFormat.java:416)
        at com.alibaba.blink.connectors.ots.OtsOutputFormat.writeAddRecord(OtsOutputFormat.java:257)
        at com.alibaba.blink.streaming.connectors.common.output.TupleRichOutputFormat.writeRecord(TupleRichOutputFormat.java:50)
        at com.alibaba.blink.streaming.connectors.common.output.TupleRichOutputFormat.writeRecord(TupleRichOutputFormat.java:34)
        at com.alibaba.blink.streaming.connectors.common.output.TupleOutputFormatAdapterSink.invoke(TupleOutputFormatAdapterSink.java:71)
        at com.alibaba.blink.streaming.connectors.common.output.TupleOutputFormatAdapterSink.invoke(TupleOutputFormatAdapterSink.java:34)
        at org.apache.flink.streaming.api.functions.sink.SinkFunction.invoke(SinkFunction.java:52)
        at org.apache.flink.streaming.api.operators.StreamSink.processElement(StreamSink.java:56)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.pushToOperator(OperatorChain.java:635)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.collect(OperatorChain.java:612)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.collect(OperatorChain.java:575)
        at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:763)
        at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:741)
        at DataStreamSinkConversion$50.processElement(Unknown Source)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.pushToOperator(OperatorChain.java:635)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.collect(OperatorChain.java:612)
        at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingWithOneInputStreamOperatorOutput.collect(OperatorChain.java:575)
        at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:763)
        at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:741)
        at org.apache.flink.table.runtime.util.StreamRecordCollector.collect(StreamRecordCollector.java:44)
        at org.apache.flink.table.runtime.aggregate.GroupAggFunction.processElement(GroupAggFunction.scala:152)
        at org.apache.flink.table.runtime.aggregate.GroupAggFunction.processElement(GroupAggFunction.scala:44)
        at org.apache.flink.table.runtime.KeyedProcessOperator.processElement(KeyedProcessOperator.java:167)
        at org.apache.flink.streaming.runtime.io.StreamInputProcessor.processRecordOrMark(StreamInputProcessor.java:286)
        at org.apache.flink.streaming.runtime.io.StreamInputProcessor.processInput(StreamInputProcessor.java:231)
        at org.apache.flink.streaming.runtime.tasks.OneInputStreamTask.run(OneInputStreamTask.java:112)
        at org.apache.flink.streaming.runtime.tasks.StreamTask.invoke(StreamTask.java:348)
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:732)
        at java.lang.Thread.run(Thread.java:834)
    ```

-   报错原因
    -   valueColumns参数中包含主键。
    -   valueColumns参数中各字段之间存在空格。
-   解决方案
    -   去除设置在valueColumns参数中的主键。
    -   删除valueColumns参数中各字段之间的空格。

## 报错：OutOfMemory

-   报错信息

    在线上阶段和调试阶段出现内存溢出（OutOfMemory）的报错。

-   报错原因
    -   调试阶段
        -   调试分配的内存不足。
        -   测试引用的源表过多。
        -   数据量过大（每个上传文件里面的测试数据均为1000条以上）。
    -   线上阶段

        作业的内存分配不足导致内存溢出。

-   解决方案
    -   调试阶段

        在不影响测试结果情况下，建议您缩减测试数据为每个文件20条左右。

    -   线上阶段

        在运行拓扑图中查看资源健康分在60分以下的节点，上调该节点的CU数。具体步骤请参看[手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)。

        ![上调CU数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85466.png)


## 报错：OutOfMemoryError: Java heap space

-   报错信息

    ![Java heap space](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85465.png)

-   报错原因

    内存不足。

-   解决方案

    在运行拓扑图中查看资源健康分在60分以下的节点，上调该节点的CU数。具体步骤请参看[手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)。

    ![上调CU数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p85466.png)


## 报错：java.lang.StackOverflowError

-   报错信息

    ```
    code:[30017], brief info:[get app plan failed], context info:[detail:[java.lang.StackOverflowError
    at java.util.HashMap.hash(HashMap.java:338)
    at java.util.HashMap.get(HashMap.java:556)
    at GeneratedMetadataHandler_RowCount.getRowCount(Unknown Source)
    at org.apache.calcite.rel.metadata.RelMetadataQuery.getRowCount(RelMetadataQuery.java:236)
    at org.apache.calcite.rel.metadata.RelMdUtil.estimateFilteredRows(RelMdUtil.java:718)
    at org.apache.calcite.rel.metadata.RelMdRowCount.getRowCount(RelMdRowCount.java:123)
    at GeneratedMetadataHandlerRowCount.getRowCount$(Unknown Source)
    at GeneratedMetadataHandler_RowCount.getRowCount(Unknown Source)
    at org.apache.calcite.rel.metadata.RelMetadataQuery.getRowCount(RelMetadataQuery.java:236)
    at org.apache.calcite.rel.metadata.RelMdRowCount.getRowCount(RelMdRowCount.java:71)
    at GeneratedMetadataHandlerRowCount.getRowCount$(Unknown Source)
    at GeneratedMetadataHandler_RowCount.getRowCount(Unknown Source)
    at org.apache.calcite.rel.metadata.RelMetadataQuery.getRowCount(RelMetadataQuery.java:236)
    at org.apache.calcite.rel.metadata.RelMdRowCount.getRowCount(RelMdRowCount.java:132)
    at GeneratedMetadataHandlerRowCount.getRowCount$(Unknown Source)
    at GeneratedMetadataHandler_RowCount.getRowCount(Unknown Source)
    at org.apache.calcite.rel.metadata.RelMetadataQuery.getRowCount(RelMetadataQuery.java:236)
    at org.apache.calcite.rel.metadata.RelMdRowCount.getRowCount(RelMdRowCount.java:71)
    at GeneratedMetadataHandlerRowCount.getRowCount$(Unknown Source)
    at GeneratedMetadataHandler_RowCount.getRowCount(Unknown Source)
    at org.apache.calcite.rel.metadata.RelMetadataQuery.getRowCount(RelMetadataQuery.java:236)     
    ```

-   报错原因

    Flink SQL语句中多次使用的UNION ALL会导致堆栈溢出。

-   解决方案

    在作业参数中添加以下的参数。

    ```
    client.jvm.option=-Xss20m
    blink.job.submit.timeoutInSeconds=300
    blink.job.option=-yD env.java.opts='-Xss20m'            
    ```


## 报错：Suspend the old job to update it

-   报错信息

    ```
    org.apache.flink.util.FlinkException: Suspend the old job to update it.
      at org.apache.flink.runtime.jobmaster.JobMaster.lambda$updateJob$16(JobMaster.java:1671)
      at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.handleRunAsync(AkkaRpcActor.java:332)
      at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.handleRpcMessage(AkkaRpcActor.java:158)
      at org.apache.flink.runtime.rpc.akka.FencedAkkaRpcActor.handleRpcMessage(FencedAkkaRpcActor.java:70)
      at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.onReceive(AkkaRpcActor.java:142)
      at org.apache.flink.runtime.rpc.akka.FencedAkkaRpcActor.onReceive(FencedAkkaRpcActor.java:40)
      at akka.actor.UntypedActor$$anonfun$receive$1.applyOrElse(UntypedActor.scala:165)
      at akka.actor.Actor$class.aroundReceive(Actor.scala:502)
      at akka.actor.UntypedActor.aroundReceive(UntypedActor.scala:95)
    ```

-   报错原因

    作业触发了AutoScale功能，AutoScale功能正在运行。

-   解决方案

    此报错不影响作业，AutoScale功能完成后，作业即可恢复运行。


## 报错：MySQLSyntaxErrorException: Unknown column

-   报错信息

    ```
    Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column 'd' in 'field list'
    ```

-   报错原因

    Flink SQL中字段的定义和您上传的表字段的定义不一致。

-   解决方案

    请您根据**Failover**界面的报错提示，修改Flink SQL字段的定义，确保和您上传的表字段的定义一致。


## 报错：TaskManager lost/killed

-   报错信息

    ```
    java.lang.Exception: The assigned slot: SimpleSlot (1) - container_e05_1505041177764_25978_01_000033 @ hdpet2mainse011132138130.et2.tbsite.net (dataPort=59317) - ALLOCATED/ALIVE is asked to release from TaskManager: container_e05_1505041177764_25978_01_000033 @ hdpet2mainse011132138130.et2.tbsite.net (dataPort=59317), probably due to TaskManager lost/killed
        at org.apache.flink.runtime.instance.SimpleSlot.releaseSlot(SimpleSlot.java:217)
        at org.apache.flink.runtime.instance.SlotPool.releaseTaskManager(SlotPool.java:699)
        at sun.reflect.GeneratedMethodAccessor44.invoke(Unknown Source)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.handleRpcInvocation(AkkaRpcActor.java:183)
        at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.handleMessage(AkkaRpcActor.java:135)
        at org.apache.flink.runtime.rpc.akka.AkkaRpcActor.access$000(AkkaRpcActor.java:72)
        at org.apache.flink.runtime.rpc.akka.AkkaRpcActor$1.apply(AkkaRpcActor.java:110)
        at akka.actor.ActorCell$$anonfun$become$1.applyOrElse(ActorCell.scala:534)
        at akka.actor.Actor$class.aroundReceive(Actor.scala:467)
        at akka.actor.UntypedActor.aroundReceive(UntypedActor.scala:97)
        at akka.actor.ActorCell.receiveMessage(ActorCell.scala:516)
        at akka.actor.ActorCell.invoke(ActorCell.scala:487)
        at akka.dispatch.Mailbox.processMailbox(Mailbox.scala:238)
        at akka.dispatch.Mailbox.run(Mailbox.scala:220)
        at akka.dispatch.ForkJoinExecutorConfigurator$AkkaForkJoinTask.exec(AbstractDispatcher.scala:397)
        at scala.concurrent.forkjoin.ForkJoinTask.doExec(ForkJoinTask.java:260)
        at scala.concurrent.forkjoin.ForkJoinPool$WorkQueue.runTask(ForkJoinPool.java:1339)
        at scala.concurrent.forkjoin.ForkJoinPool.runWorker(ForkJoinPool.java:1979)
        at scala.concurrent.forkjoin.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:107)
    ```

-   报错原因

    由于GROUP BY、JOIN等节点的state\_size默认为0，不会申请Native Memory。当节点的Native Memory没有申请或太小时，就被YARN断开了。

-   解决方案
    -   使用[手动配置调优](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业调优/手动配置调优.md)进行资源调优。
    -   配置GROUP BY、JOIN等节点的state\_size为1。每个Group，建议Native Memory配置为至少20M。

## 报错：The sts token has been expired

-   报错信息

    ```
    java.lang.RuntimeException: java.lang.RuntimeException: com.aliyun.datahub.exception.NoPermissionException: The sts token has been expired. (Status Code: 403; Error Code: NoPermission; Request ID: 201802230613304db99a0a76d8a13b)
      at com.alibaba.blink.streaming.connectors.common.reader.ParallelReader.run(ParallelReader.java:131)
      at com.alibaba.blink.streaming.connectors.common.source.AbstractParallelSource.run(AbstractParallelSource.java:107)
      at org.apache.flink.streaming.api.operators.StreamSource.run(StreamSource.java:142)
      at org.apache.flink.streaming.api.operators.StreamSource.run(StreamSource.java:78)
      at org.apache.flink.streaming.runtime.tasks.SourceStreamTask.run(SourceStreamTask.java:56)
      at org.apache.flink.streaming.runtime.tasks.StreamTask.invoke(StreamTask.java:307)
      at org.apache.flink.runtime.taskmanager.Task.run(Task.java:762)
      at java.lang.Thread.run(Thread.java:834)
    Caused by: java.lang.RuntimeException: com.aliyun.datahub.exception.NoPermissionException: The sts token has been expired. (Status Code: 403; Error Code: NoPermission; Request ID: 201802230613304db99a0a76d8a13b)
      at com.alibaba.blink.streaming.connectors.common.reader.ParallelReader.runImpl(ParallelReader.java:210)
      at com.alibaba.blink.streaming.connectors.common.reader.ParallelReader.run(ParallelReader.java:126)
      ... 7 more
    Caused by: com.aliyun.datahub.exception.NoPermissionException: The sts token has been expired. (Status Code: 403; Error Code: NoPermission; Request ID: 201802230613304db99a0a76d8a13b)
      at com.aliyun.datahub.model.serialize.JsonErrorParser.parse(JsonErrorParser.java:44)
      at com.aliyun.datahub.model.serialize.GetCursorResultJsonDeser.deserialize(GetCursorResultJsonDeser.java:19)
      at com.aliyun.datahub.model.serialize.GetCursorResultJsonDeser.deserialize(GetCursorResultJsonDeser.java:14)
      at com.aliyun.datahub.DatahubClient.getCursor(DatahubClient.java:627)
      at com.aliyun.datahub.DatahubClient.getCursor(DatahubClient.java:699)
      at com.alibaba.blink.streaming.connectors.datahub.input.RawDatahubRecordReader.seek(RawDatahubRecordReader.java:397)
      at com.alibaba.blink.streaming.connectors.datahub.input.RawDatahubRecordReader.next(RawDatahubRecordReader.java:278)
      at com.alibaba.blink.streaming.connectors.common.reader.ParallelReader$ReaderRunner.run(ParallelReader.java:341)
      at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
      at java.util.concurrent.FutureTask.run(FutureTask.java:266)
      at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1147)
      at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:622)
      ... 1 more                   
    ```

-   报错原因

    为了保证用户的数据安全，STS Token会有一定的生命周期，系统会自动重新获取Token。

-   解决方案

    建议您在With参数中设置stsUpdateSeconds='3600'。

    ```
    WITH (
        type='datahub',
        url='',
        username='',
        password='',
        tableName='',
        stsUpdateSeconds='3600'
    )             
    ```

    -   stsUpdateSeconds主动更新roleArn的时间，时间单位为秒。
    -   stsUpdateSeconds设置的值，需比各存储中，登录Session的过期时间值小。例如，如果登录Session过期时间设置为6小时，则stsUpdateSeconds参数建议设置为stsUpdateSeconds=‘10800’。

        ![stsupdate](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p86532.png)


## 报错：OTSParameterInvalid, Message: Invalid update row request: missing cells in request.

-   报错信息

    数据写入OTS结果表时，报错如下。

    ```
    Code: OTSParameterInvalid, Message: Invalid update row request: missing cells in request.
    ```

-   报错原因

    通常是因为valueColumns整行数据都为NULL。

-   排查思路

    设置结果表Type为Print，查找valueColumns整行全部都是NULL的数据。

    **说明：** 如果数据量大时，您可以添加WHERE条件快速排查valueColumns整行全部都是NULL的数据。


## 报错：There is an invalid character in tagvalue

-   报错信息

    ```
    java.lang.IllegalArgumentException: There is an invalid character in tagvalue. the tag is + <method:getFishpoolFollower$401> , the char is '$'
      at com.alibaba.hitsdb.client.value.request.Point.checkTag(Point.java:355)
      at com.alibaba.hitsdb.client.value.request.Point.checkPoint(Point.java:313)
      at com.alibaba.hitsdb.client.value.request.Point$MetricBuilder.build(Point.java:278)
      at com.alibaba.blink.connectors.hitsdb.DefaultRowPointConverter.convert(DefaultRowPointConverter.java:63)
      at com.alibaba.blink.connectors.hitsdb.HiTSDBOutputFormat.writeAddRecord(HiTSDBOutputFormat.java:130)
      at com.alibaba.blink.streaming.connectors.common.output.TupleRichOutputFormat.writeRecord(TupleRichOutputFormat.java:50)
      at com.alibaba.blink.streaming.connectors.common.output.TupleRichOutputFormat.writeRecord(TupleRichOutputFormat.java:34)
      at com.alibaba.blink.streaming.connectors.common.output.TupleOutputFormatAdapterSink.invoke(TupleOutputFormatAdapterSink.java:71)
      at com.alibaba.blink.streaming.connectors.common.output.TupleOutputFormatAdapterSink.invoke(TupleOutputFormatAdapterSink.java:34)
      at org.apache.flink.streaming.api.functions.sink.SinkFunction.invoke(SinkFunction.java:52)
      at org.apache.flink.streaming.api.operators.StreamSink.processElement(StreamSink.java:56)
      at org.apache.flink.streaming.runtime.tasks.OperatorChain$OutputAdapter.lambda$new$0(OperatorChain.java:767)
      at org.apache.flink.streaming.runtime.tasks.OperatorChain$OutputAdapter.processElement(OperatorChain.java:841)
      at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingOutput.pushToOperator(OperatorChain.java:689)
      at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingOutput.collect(OperatorChain.java:673)
      at org.apache.flink.streaming.runtime.tasks.OperatorChain$ChainingOutput.collect(OperatorChain.java:639)
      at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:1106)
      at org.apache.flink.streaming.api.operators.AbstractStreamOperator$CountingOutput.collect(AbstractStreamOperator.java:1084)
      at DataStreamSinkConversion$373.processElement(Unknown Source)
      at org.apache.flink.streaming.runtime.io.StreamInputProcessor.processInput(StreamInputProcessor.java:255)
      at org.apache.flink.streaming.runtime.tasks.OneInputStreamTask.run(OneInputStreamTask.java:89)
      at org.apache.flink.streaming.runtime.tasks.StreamTask.invoke(StreamTask.java:298)
      at org.apache.flink.runtime.taskmanager.Task.run(Task.java:747)
      at java.lang.Thread.run(Thread.java:834)
    ```

-   报错原因

    TSDB输入数据中存在非法字符`$`。

-   解决方案

    TSDB输入数据只可包含大小写英文字母、中文、数字，以及特殊字符`-`、`_`、`.`、`/`、`()`、`:`、`,`、`[]`、`=`、`‘`、`#`。详情请参见[TSDB请求参数输入限制](https://help.aliyun.com/document_detail/59939.html?spm=a2c4g.11186623.6.615.5c1762bfrDgXOO)。

    **说明：** 建议您在写入数据前进行字符替换。


## 报错：Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction

-   报错信息

    写MySQL（TDDL/RDS）时，出现死锁（DeadLock）。

    **说明：** 在实时计算Flink中，下游数据库使用MySQL等关系数据库（对应的Connector为TDDL/RDS），当实时计算Flink版频繁写某个表或者资源时，存在死锁风险。

-   死锁形成的示例

    假设完成一次INSERT需要依次抢占\(A,B\)2个锁。A是一个范围锁，有2个事务\(T1,T2\)，表的Schema为\(id\(自增主键\),nid\(唯一键\)\)。T1包含2条insert\(null,2\),\(null,1\)，T2包含1条insert\(null,2\)。

    1.  t时刻，T1第一条INSERT插入，此时T1持有\(A,B\)2个锁。
    2.  t+1时刻T2开始插入，需要等待锁A来锁住\(-inf,2\]，此时A被T1拥有，且锁住了\(-inf,2\]，区间存在包含关系，所以T2依赖T1释放A。
    3.  t+2时刻T1第二条INSERT执行，需要A锁住\(-inf,1\]，该区间属于\(-inf,2\]，所以需要排队等T2释放锁，所以T1依赖T2释放A。
    当T1和T2相互依赖且相互等待时死锁形成。

-   RDS/TDDL、OTS数据库引擎锁的区别
    -   **RDS/TDDL**：InnoDB的行锁是针对索引加的锁，不是针对单条记录加的锁。所以虽然是访问不同行的记录，但是如果使用相同的索引键，会出现锁冲突，造成了整个区域的数据都无法更新。
    -   **OTS**：单行锁，不影响其他数据更新。
-   死锁的解决方案

    高QPS/TPS或高并发写入情况场景，建议使用OTS作为结果表，可以解决死锁的问题。通常，不建议使用TDDL或者RDS作为Flink Job的结果表。

    如果必须要使用MySQL等关系数据库作为Sink节点，有以下建议：

    -   确保没有其他读写业务方的干扰。
    -   如果Job的数据量不大可以尝试单并发写入。但是在高QPS/TPS、高并发情况下，写入性能会降低。
    -   尽可能不使用UniqueKey（唯一主键），带UniqueKey表的写入可能会导致死锁。如果业务要求表必须包含UniqueKey，请按照字段区分能力从大到小排列来定义UniqueKey，可以大幅降低死锁出现概率。例如，您可以把MD5函数放在day\_time\(20171010\)前面，就可以使得字段区分能力从大到小排列来定义UniqueKey，从而解决死锁问题。
    -   根据业务特点做分库分表，尽可能避免单表写入，实施细节请联系对应的数据库管理员。

## 报错：Value '0000-00-00 00:00:00' can not be represented as java.sql.Timestamp

-   报错信息

    ```
    Caused by: java.sql.SQLException: Value '0000-00-00 00:00:00' can not be represented as java.sql.Timestamp
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:964)
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:897)
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:886)
        at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:860)
        at com.mysql.jdbc.ResultSetRow.getTimestampFast(ResultSetRow.java:937)
        at com.mysql.jdbc.ByteArrayRow.getTimestampFast(ByteArrayRow.java:130)
        at com.mysql.jdbc.ResultSetImpl.getTimestampInternal(ResultSetImpl.java:5918)
        at com.mysql.jdbc.ResultSetImpl.getTimestamp(ResultSetImpl.java:5588)
        at com.alibaba.druid.pool.DruidPooledResultSet.getTimestamp(DruidPooledResultSet.java:220)
        at com.alibaba.blink.connectors.mysql.dim.MySqlRowFetcherBase.deserializeToInnerObject(MySqlRowFetcherBase.java:316)
        at com.alibaba.blink.connectors.mysql.dim.MySqlRowFetcherBase.toResultRow(MySqlRowFetcherBase.java:251)
        at com.alibaba.blink.connectors.mysql.dim.MySqlRowFetcherBase.flatMap(MySqlRowFetcherBase.java:218)
        at com.alibaba.blink.connectors.mysql.dim.MySqlRowFetcherBase.flatMap(MySqlRowFetcherBase.java:58)
        at com.alibaba.blink.streaming.connectors.common.LookupFunctionWrapper.eval(LookupFunctionWrapper.java:56)
        at LookupFunction$146.flatMap(Unknown Source)
        at org.apache.flink.table.runtime.join.TemporalTableJoinProcessRunner.processElement(TemporalTableJoinProcessRunner.scala:85)
        at org.apache.flink.table.runtime.join.TemporalTableJoinProcessRunner.processElement(TemporalTableJoinProcessRunner.scala:33)
        at org.apache.flink.streaming.api.operators.ProcessOperator.processElement(ProcessOperator.java:66)
    ```

-   问题原因

    RDS维表中TIMESTAMP类型的字段中存在非法格式的数据0000-00-00 00:00:00，维表JOIN非法格式的数据就会报错。

-   解决方案

    在RDS维表WITH参数`url`后添加`?zeroDateTimeBehavior=convertToNull`，输出为null。


## 报错：Failover:offset \(0\) + length \(8\) exceed the capacity of the array: 1

-   报错信息

    ```
    Caused by: java.lang.IllegalArgumentException: offset (0) + length (8) exceed the capacity of the array: 1
        at org.apache.hadoop.hbase.util.Bytes.explainWrongLengthOrOffset(Bytes.java:548)
        at org.apache.hadoop.hbase.util.Bytes.toLong(Bytes.java:522)
        at org.apache.hadoop.hbase.util.Bytes.toLong(Bytes.java:495)
    ```

-   问题原因

    云数据库HBase版的数据类型定义错误。

-   解决方案

    修改云数据库HBase版的数据类型和物理表一致。


## 作业启动一段时间后报错停止，Failover里出现报错信息，应该如何处理？

-   STS Token过期

    ![sts token过期](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0262749951/p86854.png)

    STS Token过期导致的报错，不影响线上作业。详情请参见[报错：The sts token has been expired](#section_lh4_3oq_a3r)。

-   脏数据处理失败
    -   排错指引

        在Failover中查看脏数据具体内容。

    -   解决方案

        修改SQL，过滤脏数据后，重新[上线](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/作业开发/上线.md)作业。

-   写入MYSQL类型数据库死锁
    -   排错指引
        -   手动调优后，检查Sink节点拆分后的部分节点与同时写入的其他节点之间，是否出现了Rebalance。
        -   检查Sink节点是否多并发写入数据库。
        -   检查出现死锁的表中PK与数据库表定义的UK是否一致，包括顺序。
        -   检查定义的PK是否按照区分度高低进行排列。
    -   解决方案

        详情请参见[写MySQL（TDDL/RDS）时，出现死锁（DeadLock）](#section_vxa_dlo_11h)。


