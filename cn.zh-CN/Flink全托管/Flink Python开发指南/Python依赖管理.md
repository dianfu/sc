---
keyword: [Flink全托管, 开发, DataStream, Table API]
---

# 作业开发

本文为您介绍Flink全托管DataStream API、Table API和Python API作业开发的限制说明和开发方法。

## DataStream API或Table API作业开发

-   限制说明

    由于**Flink全托管**产品受部署环境、网络环境等因素的影响，所以开发**Flink全托管**DataStream或Table API作业，需要注意以下限制：

    -   仅支持开源Flink V1.10和Flink V1.11版本。
    -   仅支持JAR形式的作业提交和运行。
    -   仅支持运行单个JAR形式的作业。
    -   不支持在Main函数中读取本地配置。
    -   **Flink全托管**运行环境使用的是JDK1.8，请使用JDK1.8进行作业开发。
    -   仅支持开源Scala V2.11版本。
-   Connector使用

    Maven中央库中已经放置了VVR Connector，您需要在Maven POM文件中添加您需要的Connector作为项目依赖，示例如下。

    ```
    <dependencies>
        <dependency>
            <groupId>com.alibaba.ververica</groupId>
            <artifactId>${connector.type}</artifactId>
            <version>${connector.version}</version>
        </dependency>
    </dependencies>
    ```

    每个Connector版本对应的Connector类型可能不同，建议您使用最新版本。Connector版本、VVR/Flink版本和Connector类型的对应关系如下表所示，具体可查看不同版本的新功能发布记录。

    **说明：** 您需要在SNAPSHOT仓库（oss.sonatype.org）查找带SNAPSHOT的Connector版本， 在Maven中央库（search.maven.org ）上会查找不到。

    |Connector版本|VVR/Flink版本|Connector类型|
    |-----------|-----------|-----------|
    |1.11-vvr-2.1.3-SNAPSHOT|VVR 2.1.3（对应Flink 1.11.3）|    -   ververica-connector-common
    -   ververica-connector-random
    -   ververica-connector-datahub
    -   ververica-connector-odps
    -   ververica-connector-cloudhbase
    -   ververica-connector-phoenix
    -   ververica-connector-redis
    -   ververica-connector-elasticsearch
    -   ververica-connector-mongodb
    -   ververica-connector-hologres
    -   ververica-connector-jdbc
    -   ververica-connector-ads
    -   ververica-connector-adb-3.0
    -   ververica-connector-rds
    -   ververica-connector-ots
    -   ververica-connector-elasticsearch6
    -   ververica-connector-elasticsearch7
    -   ververica-connector-kafka
    -   ververica-connector-hadoop-shaded
    -   ververica-connector-cloudhbase-shaded
    -   ververica-connector-mysql
    -   ververica-connector-postgres
    -   ververica-connector-hive-2.3.6
    -   ververica-connector-hive-3.1.2
    -   ververica-connector-continuous-odps |
    |1.11-vvr-2.1.2-SNAPSHOT|VVR 2.1.2（对应Flink 1.11.2）|    -   ververica-connector-common
    -   ververica-connector-random
    -   ververica-connector-datahub
    -   ververica-connector-odps
    -   ververica-connector-cloudhbase
    -   ververica-connector-phoenix
    -   ververica-connector-redis
    -   ververica-connector-elasticsearch
    -   ververica-connector-mongodb
    -   ververica-connector-hologres
    -   ververica-connector-jdbc
    -   ververica-connector-ads
    -   ververica-connector-adb-3.0
    -   ververica-connector-rds
    -   ververica-connector-ots
    -   ververica-connector-elasticsearch6
    -   ververica-connector-elasticsearch7
    -   ververica-connector-kafka
    -   ververica-connector-hadoop-shaded
    -   ververica-connector-cloudhbase-shaded
    -   ververica-connector-mysql
    -   ververica-connector-postgres
    -   ververica-connector-hive-2.3.6
    -   ververica-connector-hive-3.1.2
    -   ververica-connector-continuous-odps |

-   作业开发

    您需要在线下完成作业开发后，再在**Flink全托管**控制台上提交作业到集群上运行。您可以参见以下文档开发**Flink全托管**产品业务代码：

    -   Flink Datastream和TableAPI Connector依赖，请参见[Connector依赖](http://oss.sonatype.org/)。
    -   Apache Flink是什么，以及它的体系架构、应用程序和特性功能等，请参见[Apache Flink介绍](https://flink.apache.org/flink-architecture.html)。
    -   Apache Flink V1.10业务代码开发，请参见[Flink DataStream API开发指南](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/datastream_api.html)和[Flink Table API & SQL开发指南](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/table/)。
    -   Apache Flink的编码、Java语言、Scala语言、组件和格式等指南，请参见[代码风格和质量指南](https://flink.apache.org/contributing/code-style-and-quality-preamble.html)。
    -   Apache Flink编码过程中遇到的问题及解决方法，请参见[常见问题](https://flink.apache.org/gettinghelp.html)。
    为了避免JAR依赖冲突，您需要注意以下几点：

    -   Flink镜像和Pom依赖Flink版本请保持一致。
    -   请不要上传Runtime层的JAR，即在依赖中添加`<scope>provided</scope>`。
    -   其他第三方依赖请采用Shade方式打包，Shade打包详情参见[Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/index.html)。
    Flink 依赖冲突问题，详情请参见[如何解决Flink依赖冲突问题？](/cn.zh-CN/Flink全托管/常见问题.md)


## Python API作业开发

-   限制说明
    -   仅支持开源Flink V1.11版本。
    -   Flink全托管集群已预装了Python版本为Python 3.7，且Python环境中已经预装了Pandas、NumPy、PyArrow等常用的Python库。因此需要您在Python 3.7版本编辑代码。
-   作业开发

    您需要在线下完成Python API作业开发后，再在**Flink全托管**控制台上提交作业到集群上运行。您可以参见以下文档开发**Flink全托管**产品业务代码：

    -   Apache Flink V1.11业务代码开发，请参见[Flink Python Table API开发指南](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/python/table-api-users-guide/intro_to_table_api.html)。
    -   Apache Flink编码过程中遇到的问题及解决方法，请参见[常见问题](https://flink.apache.org/gettinghelp.html)。

