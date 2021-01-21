---
keyword: [创建Package, IntelliJ IDEA, 自定义函数]
---

# 使用IntelliJ IDEA开发自定义函数

本文为您介绍如何使用IntelliJ IDEA开发实时计算Flink版自定义函数，包括搭建开发环境和实时计算Flink版作业中引用自定义函数。

**说明：**

-   仅独享模式支持自定义函数功能。
-   请使用[IntelliJ IDEA工具](https://www.jetbrains.com/idea/download/#section=mac)开发自定义函数。

## 配置Maven

1.  下载Maven。

    1.  登录[Maven官网下载页面](http://maven.apache.org/download.cgi)。

    2.  下载apache-maven-3.5.3-bin.tar.gz。

    3.  解压下载的安装包到指定目录，例如：/Users/<userName\>/Documents/maven。

2.  配置环境变量。

    1.  在Terminal中，执行`vim ~/.bash_profile`命令。

    2.  在.bash\_profile文件中添加如下命令。

        ```
        export M2_HOME=/Users/<userName>/Documents/maven/apache-maven-3.5.3
        export PATH=$PATH:$M2_HOME/bin
        ```

    3.  保存并关闭.bash\_profile文件。

    4.  执行`source ~/.bash_profile`命令使配置生效。

3.  执行`mvn -v`命令查看配置是否生效。

    如果打印如下信息，则配置生效。

    ```
    Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
    Maven home: /Users/<userName>/Documents/maven/apache-maven-3.5.0
    Java version: 1.8.0_121, vendor: Oracle Corporation
    Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre
    Default locale: zh_CN, platform encoding: UTF-8
    OS name: "mac os x", version: "10.12.6", arch: "x86_64", family: "mac"
    ```


## 搭建开发环境

1.  下载[UDX示例](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/自定义函数（UDX）/概述.md)。

2.  在Linux环境下解压下载文件。

    ```
    tar xzvf RealtimeCompute-udxDemo.gz
    ```

3.  打开**IntelliJ IDEA**，单击**Open**打开下载的UDX示例。

    ![IntelliJ IDEA](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9684359951/p34496.png)


## 实时计算Flink版作业引用JAR包

1.  创建Package。

    1.  右键单击**java** \> **New** \> **Package**。

        ![java中创建Package](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9684359951/p34497.png)

    2.  在**New Package**中输入Package名称。本文以`com.hjc.test.blink.sql.udx`为例。

    3.  单击**OK**。

2.  创建Class。

    1.  右键单击**com.hjc.test.blink.sql.udx** \> **New** \> **Java Class**。

    2.  在**Create New Class**中，输入Class名称。

        **说明：** **Kind**保持默认选择**Class**。

    3.  单击**OK**。

3.  在Class中输入以下代码。

    ```
    package com.hjc.test.blink.sql.udx;
    
    import org.apache.flink.table.functions.FunctionContext;
    import org.apache.flink.table.functions.ScalarFunction;
    
    public class StringLengthUdf extends ScalarFunction {
        // 可选，open方法可以不编写。
        // 如果编写open方法，需要声明'import org.apache.flink.table.functions.FunctionContext;'。
        @Override
        public void open(FunctionContext context) {
            }
        public long eval(String a) {
            return a == null ? 0 : a.length();
        }
        public long eval(String b, String c) {
            return eval(b) + eval(c);
        }
        //可选，close方法可以不编写。
        @Override
        public void close() {
            }
    }
    ```

4.  在Terminal中，执行`mvn package`或`mvn assembly:assembly`，将项目写入JAR包。

    **说明：**

    -   如果需要将第三方依赖写入JAR包，请使用`mvn assembly:assembly`。
    -   编译后的JAR包为RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT.jar或RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT-jar-with-dependencies.jar（将第三方依赖写入JAR包）。
5.  实时计算Flink版作业引用JAR包。

    1.  登录[实时计算控制台](https://stream.console.aliyun.com)。

    2.  在顶部菜单中，单击**开发**。

    3.  在左侧的导航栏中，单击**资源引用**。

    4.  在**资源引用**页签的右上角，单击**新建资源**。

        |参数名称|说明|
        |----|--|
        |上传方式|实时计算控制台上仅支持本地上传。**说明：** 本地上传JAR包的大小上限为300 MB。如果JAR包大小超过300 MB，请在集群绑定的OSS控制台上，或通过OpenAPI的方式上传JAR包。 |
        |资源选择|单击**选择资源**，选择需要引用的资源。|
        |资源名称|输入资源名称。|
        |资源备注|输入资源备注信息。|
        |资源类型|选择引用资源类型，JAR、DICTIONARY或PYTHON。|

    5.  在**资源引用**页签中，鼠标悬停在对应作业的右侧的**更多**上。

    6.  在下拉列表中，选择**引用**。

    7.  在作业的编辑窗口顶部，输入自定义函数声明，示例如下。

        ```
        CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
        ```


