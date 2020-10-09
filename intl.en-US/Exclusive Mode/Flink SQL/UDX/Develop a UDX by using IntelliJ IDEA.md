---
keyword: [create a package, IntelliJ IDEA, UDX]
---

# Develop a UDX by using IntelliJ IDEA

This topic describes how to develop a user-defined extension \(UDX\) in Realtime Compute for Apache Flink by using IntelliJ IDEA. The development process includes building a development environment and referencing a UDX in a Realtime Compute for Apache Flink job.

**Note:**

-   Only Realtime Compute for Apache Flink in exclusive mode supports UDXs.
-   We recommend that you use [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=mac) to develop a UDX.

## Configure Maven

1.  Download a Maven installation package.

    1.  Go to the [download page at the official Apache Maven website](http://maven.apache.org/download.cgi).

    2.  Download apache-maven-3.5.3-bin.tar.gz.

    3.  Decompress the downloaded package to a specified directory, such as /Users/<userName\>/Documents/maven.

2.  Configure environment variables.

    1.  In the terminal, run the `vim ~/.bash_profile` command.

    2.  In the .bash\_profile file, add the following commands:

        ```
        export M2_HOME=/Users/<userName>/Documents/maven/apache-maven-3.5.3
        export PATH=$PATH:$M2_HOME/bin
        ```

    3.  Save and close the .bash\_profile file.

    4.  Run the `source ~/.bash_profile` command for the configuration to take effect.

3.  Run the `mvn -v` command to check whether the configuration takes effect.

    If information similar to the following is displayed, the configuration takes effect:

    ```
    Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
    Maven home: /Users/<userName>/Documents/maven/apache-maven-3.5.0
    Java version: 1.8.0_121, vendor: Oracle Corporation
    Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre
    Default locale: zh_CN, platform encoding: UTF-8
    OS name: "mac os x", version: "10.12.6", arch: "x86_64", family: "mac"
    ```


## Build a development environment

1.  Download the [UDX demo](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md).

2.  Decompress the downloaded package in a Linux operating system.

    ```
    tar xzvf RealtimeCompute-udxDemo.gz
    ```

3.  Open **IntelliJ IDEA** and click **Open** to open the demo.

    ![IntelliJ IDEA](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4440246951/p34496.png)


## Reference a JAR package in a job

1.  Create a package.

    1.  Right-click **java** and navigate to **New** \> **Package**.

        ![Create a package in Java](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4440246951/p34497.png)

    2.  In the **New Package** dialog box, enter a package name. In this example, a package named `com.hjc.test.blink.sql.udx` is created.

    3.  Click **OK**.

2.  Create a class.

    1.  Right-click **com.hjc.test.blink.sql.udx** and navigate to **New** \> **Java Class**.

    2.  In the **Create New Class** dialog box, enter a class name.

        **Note:** Retain the default value **Class** of **Kind**.

    3.  Click **OK**.

3.  Enter the following code in the class:

    ```
    package com.hjc.test.blink.sql.udx;
    
    import org.apache.flink.table.functions.FunctionContext;
    import org.apache.flink.table.functions.ScalarFunction;
    
    public class StringLengthUdf extends ScalarFunction {
        // The open method is optional.
        // To write the open method, you must add 'import org.apache.flink.table.functions.FunctionContext;' to the code.
        @Override
        public void open(FunctionContext context) {
            }
        public long eval(String a) {
            return a == null ? 0 : a.length();
        }
        public long eval(String b, String c) {
            return eval(b) + eval(c);
        }
        // The close method is optional.
        @Override
        public void close() {
            }
    }
    ```

4.  In the terminal, run the `mvn package` or `mvn assembly:assembly` command to add the project to the JAR package.

    **Note:**

    -   If you need to add a third-party dependency to the JAR package, run the `mvn assembly:assembly` command.
    -   The compiled JAR package is RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT.jar or RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT-jar-with-dependencies.jar. It contains a third-party dependency.
5.  Reference the JAR package in a Realtime Compute for Apache Flnik job.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://stream.console.aliyun.com).

    2.  In the top navigation bar, click **Development**

    3.  In the left-side navigation pane, click the **Resources** tab.

    4.  In the upper-right corner of the **Resources** pane, click **Create Resource**.

        |Parameter|Description|
        |---------|-----------|
        |Location|You can only upload JAR packages from your machine in the Realtime Compute for Apache Flink console.**Note:** The maximum size of a JAR package that can be uploaded from your machine is 300 MB. If the JAR package exceeds 300 MB, you must upload it to the Object Storage Service \(OSS\) bucket bound to your cluster or use APIs to upload it. |
        |Resource|Click **Upload Resource** and select the resource that you want to reference.|
        |Resource Name|Enter a name for the resource.|
        |Resource Description|Enter a resource description.|
        |Resource Type|Select the type of the resource. The type can be JAR, DICTIONARY, or PYTHON.|

    5.  In the **Resources** pane, find the new resource, and move the pointer over **More** in the Actions column.

    6.  In the drop-down list, select **Reference**.

    7.  In the code editor, declare the UDX at the beginning. The following code is an example:

        ```
        CREATE FUNCTION stringLengthUdf AS 'com.hjc.test.blink.sql.udx.StringLengthUdf';
        ```


