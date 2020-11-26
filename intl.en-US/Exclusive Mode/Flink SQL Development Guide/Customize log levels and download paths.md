# Customize log levels and download paths

You can configure the parameters of a Realtime Compute for Apache Flink job to customize the log download levels and download paths.

**Note:** Only Realtime Compute for Apache Flink V3.2 and later allows you to customize the log levels and paths.

## Procedure

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the **Development** section of the left-side navigation pane, double-click the folder that stores the required job and find the job.
4.  Double-click the job to go to the job editing page.
5.  On the right side of the job editing page, click the **Parameters** tab. In the pane that appears, enter the configuration data of Log4j.

    |Log4j configuration parameter|Description|
    |-----------------------------|-----------|
    |Root logger|Processes some operations for logging. The syntax of the root logger is `log4j.rootLogger = [ level ] , appenderName, appenderName, …`. The syntax indicates that the log records whose level is at least the specified level are delivered to one or more destinations. The following items describe the parameters in the syntax:     -   level: specifies the level of logs. The levels of logs include ERROR, WARN, INFO, and DEBUG that are sorted in descending order. ERROR indicates serious errors for the job. WARN indicates potentially harmful situations for the job. INFO indicates informational messages for the job. DEBUG indicates the debugging information about the job.
    -   appenderName: specifies the name of the destination to which log records are delivered. You can specify multiple appenders for the root logger. |
    |Appender|Specifies the destination to which log records are delivered. To define an appender, use the following syntax:     ```
log4j.appender.appenderName = fully.qualified.name.of.appender.class
log4j.appender.appenderName.option1 = value1
...
log4j.appender.appenderName.optionN = valueN
    ```

Appenders have the following two types:

    -   Appenders provided by Log4j
        -   `org.apache.log4j.ConsoleAppender`: delivers log records to a console.
        -   `org.apache.log4j.FileAppender`: delivers log records to a file.
        -   `org.apache.log4j.DailyRollingFileAppender`: generates a log file every day.
        -   `org.apache.log4j.RollingFileAppender`: generates a new file when a file reaches its maximum size.
        -   `org.apache.log4j.WriterAppender`: delivers log records in the stream format to a specified destination.
    -   Custom appenders

Custom appenders allow you to deliver log records to only the Log Service and Object Storage Service \(OSS\) services. The classes of the two services are fixed. The following example lists the fixed classes.

        -   Log Service: `log4j.appender.loghub=com.alibaba.blink.log.loghub.BlinkLogHubAppender`
        -   OSS: `log4j.appender.oss=com.alibaba.blink.log.oss.BlinkOssAppender`
You can also specify other configuration items that are supported by the Log4j syntax.

For more information, see [Change the log level to DEBUG and deliver log records to an OSS bucket.](#section_r62_ub6_xmu). |

    **Note:** After you specify the job parameters, restart the job. You can view the new logs in the OSS bucket.

6.  **Terminate** the job. For more information, see [Terminate a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Terminate a job.md).
7.  **Start** the job. For more information, see [Start a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Start a job.md).

## Considerations

-   The OSS path must be the same as that for the Realtime Compute for Apache Flink cluster that is deployed in exclusive mode.
-   **The logs that you can download** are provided through Log4j. system.out logs are not included in these logs.
-   The specified Log Service or OSS can interact with the cluster where the job resides.
-   The job can be started in most cases even if the custom log output configuration is invalid. However, the logs of the job cannot be displayed based on the configuration.

## Change the log level to DEBUG and deliver log records to an OSS bucket.

**Note:** If you specify the log4j.rootLogger parameter, you may fail to view log information or troubleshoot related issues on the Realtime Compute for Apache Flink development platform. Use this parameter with caution.

```
#Change the log level to DEBUG and export logs to a specific file in an OSS bucket.
log4j.rootLogger=DEBUG, file, oss

#This parameter setting is fixed. You do not need to change the setting. Configure the appender class for OSS.
log4j.appender.oss=com.alibaba.blink.log.oss.BlinkOssAppender

#The endpoint.
log4j.appender.oss.endpoint=oss-cn-hangzhou****.aliyuncs.com

#The AccessKey ID.
log4j.appender.oss.accessId=U****4ZF  

#The AccessKey secret.
log4j.appender.oss.accessKey=hsf****DeLw   

#The OSS bucket name.
log4j.appender.oss.bucket=et**** 

#The subdirectory that is used to store logs.           
log4j.appender.oss.subdir=/luk****/test/
```

## Disable the output of the logs for a specified package, and deliver logs to a specified Logstore of Log Service.

```
#Disable the output of the logs for the log4j.logger.org.apache.hadoop package.
log4j.logger.org.apache.hadoop = OFF

#This parameter setting is fixed. You do not need to change the setting. Configure the LogHub appender.       
log4j.appender.loghub = com.alibaba.blink.log.loghub.BlinkLogHubAppender

#Deliver only logs of the ERROR level to Log Service.
log4j.appender.loghub.Threshold = ERROR

#The name of a project in Log Service.
log4j.appender.loghub.projectName = blink-errdumpsls-test

#The name of a Logstore in Log Service.
log4j.appender.loghub.logstore = logstore-3

#The endpoint of Log Service.
log4j.appender.loghub.endpoint = http://cn-shanghai****.sls.aliyuncs.com

#The AccessKey ID.
log4j.appender.loghub.accessKeyId = Tq****WR

#The AccessKey secret.
log4j.appender.loghub.accessKey = MJ****nfVx
```

## Change the log level to WARN, and disable the output of the logs for a specified package.

```
#Change the log level to WARN.
log4j.rootLogger=WARN,file

#Disable the output of the logs for the log4j.logger.org.apache.hadoop package.
log4j.logger.org.apache.hadoop = OFF
```

