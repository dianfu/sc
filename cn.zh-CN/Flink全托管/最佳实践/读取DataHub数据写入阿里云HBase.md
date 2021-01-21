---
keyword: [读取DataHub, 写入HBase]
---

# 读取DataHub数据写入阿里云HBase

本文以Windows系统，Flink 1.10版本为例，为您介绍如何使用Flink作业实时同步DataHub数据至阿里云HBase。

-   开发工具
    -   本地安装了Java JDK 8。
    -   本地安装了Maven 3.x。
    -   本地安装了用于Java或Scala开发的IDE，推荐IntelliJ IDEA，且已配置完成JDK和Maven环境。
-   开通产品
    -   开通了专有网络VPC，详情请参见[创建专有网络](/cn.zh-CN/专有网络和交换机/管理专有网络/创建专有网络.md)。
    -   开通了对象存储OSS，详情请参见[开通OSS服务](https://help.aliyun.com/document_detail/31884.html#task-njz-hf4-tdb)。
    -   开通了Flink全托管产品，详情请参见[开通流程](/cn.zh-CN/Flink全托管/开通流程.md)。
    -   开通了与Flink全托管同一地域下相同VPC的DataHub，详情请参见[DataHub产品首页](https://www.aliyun.com/product/datahub?spm=5176.124785.J_8058803260.214.76c83094YQ0pel)。
    -   开通了与Flink全托管同一地域下相同VPC的HBase实例（本示例为标准版HBase），详情请参见[HBase产品首页](https://www.aliyun.com/product/hbase?spm=a2c4g.11174283.2.1.7469363fCl3WwP)。
-   权限配置

    您已将Flink全托管集群IP需要添加至HBase白名单。详情请参见[设置白名单](/cn.zh-CN/Flink全托管/准备工作/设置白名单.md)。

-   上下游存储
    -   在DataHub上已创建了项目和Topic，本示例Topic中有a、b和c三个字段，数据类型依次为BOOLEAN、STRING和STRING。
    -   在HBase上已创建了表hbase\_sink和列簇f1。详情请参见[使用Shell访问](https://help.aliyun.com/document_detail/52056.html?spm=a2c4g.11174283.6.595.ccba363fBF1uOn)和[创建表和列簇](http://hbase.apache.org/1.1/book.html?spm=a2c4g.11186623.2.18.6abe5cc1OSJzJj#shell_exercises)。

## 作业开发

1.  下载并解压[Hbase\_ASI\_Demo-main](https://github.com/RealtimeCompute/Hbase_ASI_Demo)示例到本地。

2.  在Intellij IDEA中，单击**file** \> **open**，打开刚才解压缩完成的Hbase\_ASI\_Demo-main。

3.  双击打开\\Hbase\_ASI\_Demo-main\\src\\main\\java\\Hbase\_Demo后，修改HbaseDemo.java文件中的DataHub与HBase相关参数。

    ```
    //DataHub相关参数
    //private static String endPoint ="public endpoint";//公网访问（填写内网Endpoint，就不用填写公网Endpoint）。
    private static String endPoint = "inner endpoint";//内网访问。
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;//设置消费的启动位点对应的时间。
    //HBase相关参数
    private static String zkQuorum = "yourZK";
    private static String tableName = "yourTable";
    private static String columnFamily = "yourcolumnFamily";
    ```

    |存储类型|参数|说明|
    |----|--|--|
    |DataHub|endPoint|替换成您DataHub的endPoint。**说明：** 如果填写内网Endpoint，就不用填写公网Endpoint。 |
    |projectName|替换成您DataHub的Project名称。|
    |topicSourceName|替换成您DataHub的Topic名称。|
    |accessId|替换成您阿里云账号的AccessKey ID。|
    |accessKey|替换成您阿里云账号的AccessKey Secret。|
    |HBase|zkQuorum|替换成您HBase的**ZK链接地址**。您可以通过以下方式查看**ZK链接地址**信息：    -   在HBase控制台目标实例详情左侧导航栏，单击**数据库连接**后，在**连接信息**区域查看。
    -   在HBase安装目录/conf/hbase-site.xml文件中查看hbase.zookeeper.quorum相关配置。 |
    |tableName|替换成您创建的HBase表名。|
    |columnFamily|替换成您创建的HBase表列簇名。|

4.  在下载文件中pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn package -Dcheckstyle.skip
    ```

    根据您pom.xml文件中artifactId的信息，\\Hbase\_ASI\_Demo-main\\target\\目录下会出现Hbase\_Demo-1.0-SNAPSHOT.jar的JAR包，即代表完成了开发工作。


## 作业提交

请参见[作业提交](/cn.zh-CN/Flink全托管/Flink Datastream开发指南/作业提交.md)完成作业上线。

## 查看结果

1.  构建测试数据。

    在Flink全托管新建并启动SQL作业，将结果数据发送至DataHub中，作为该最佳实践的测试数据。

    ```
    CREATE TEMPORARY TABLE datagen_source (
       a BOOLEAN
    ) WITH (
       'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE datahub_sink (
       a BOOLEAN,
       b VARCHAR,
       c VARCHAR
    ) WITH (
        'connector' = 'datahub',
        'endPoint' = '<yourEndpoint>',    --替换为您开通的Datahub服务的endPoint。
        'project' = '<yourProject>',   --替换为您开通的Datahub服务的Project名称。
        'topic' = '<yourTopic>',   --替换为您开通的Datahub服务Topic名称。
        'accessId'='<yourAccessId>',  --替换为阿里云账号的AccessKey ID。   
        'accessKey'='<yourAccessKey>'  --替换为阿里云账号的AccessKey Secret。
    );
    
    INSERT INTO datahub_sink
    SELECT
        a,'rowkey3' as b,'123' as c
    FROM datagen_source;
    ```

2.  连接HBase集群。

    详情请参见[使用Shell访问](https://help.aliyun.com/document_detail/52056.html?spm=a2c4g.11174283.6.595.ccba363fBF1uOn)。

3.  执行`scan 'hbase_sink'`查看写入数据。

    出现类似如下输出，则表示已经成功将DataHub数据写入阿里云HBase。

    ![验证结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8789287951/p139880.png)


