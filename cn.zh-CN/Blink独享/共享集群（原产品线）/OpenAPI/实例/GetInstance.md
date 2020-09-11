# GetInstance

您可以通过GetInstance获取运行作业的全部信息，未运行的作业调用该接口会失败。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetInstance&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/jobs/[jobName]/instances/[instanceId] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|instanceId|Long|是|123|实例ID，可以通过ListInstance接口或者StartJob接口获取。 |
|jobName|String|是|job1|作业名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|是|cn-hangzhou-pre|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Instance|Struct| |Instance详情 |
|ActualState|String|RUNNING|Instance的实际状态：

 -   WAITING：等待。
-   RUNNING：运行中。
-   PAUSED：暂停。
-   TERMINATED：停止。
-   SUCCESS：成功（批作业）。
-   FAILED：失败（批作业）。 |
|ApiType|String|DATASTREAM|作业类型：

 -   DATASTREAM：Datastream作业。
-   SQL：SQL作业。 |
|AutoScaleParams|String|"\{\\"isOnOff\\":false,\\"strategyConfigure\\":null,\\"maxCU\\":null\}|作业上线时设置的AutoScale参数。 |
|ClusterId|String|rcmp9x37ztfb63g1x7lt\*\*\*\*|集群ID |
|Code|String|--完整主类名，必填，例如com.alibaba.realtimecompute.DatastreamExample\\nblink.main.class=Hbase\_Demo.HbaseDemo\\n\\n--包含完整主类名的JAR包资源名称，多个JAR包时必填，例如blink\_datastream.jar\\n--blink.main.jar=hbase\_demo-1.0-snapshot.jar \\n\\n--默认state backend配置，当作业代码没有显式声明时生效\\nstate.backend.type=niagara\\nstate.backend.niagara.ttl.ms=129600000\\n\\n--默认Checkpoint配置，当作业代码没有显式声明时生效\\nblink.checkpoint.interval.ms=180000\\n\\n--默认启用项目参数\\n--enable.project.config=true|Instance的运行代码：

 -   SQL作业返回SQL代码。
-   Datastream作业返回Datastream配置。 |
|EndTime|Long|1548397575000|作业结束时间，未结束作业返回空值。 |
|EngineJobHandler|String|application\_1597654819348\_\*\*\*\*\|405a6d40d7f4f2618ed532359887\*\*\*\*|Instance在YARN中的名称，格式为： ApplicaitonID \| jobID（JobManager分配的jobid）。 |
|EngineVersion|String|blink-3.4.4|Blink版本 |
|ExpectState|String|RUNNING|期望状态：

 -   RUNNING：运行中。
-   PAUSED：暂停。
-   TERMINATED：停止。
-   SUCCESS：成功（批作业）。
-   FAILED：失败（流作业）。 |
|Id|Long|412536|InstanceID |
|InputDelay|Long|-99999999|业务延迟，单位为毫秒。 |
|JobName|String|job1|作业名称 |
|JobType|String|FLINK\_STREAM|作业类型：

 -   FLINK\_STREAM：流作业。
-   FLINK\_BATCH：批作业。 |
|LastErrorMessage|String|error|最新的报错信息 |
|LastErrorTime|Long|1548397575000|最新的报错时间 |
|LastOperateTime|Long|1599794334000|最新的操作时间 |
|LastOperator|String|239960377092765296|最新的操作人 |
|Packages|String|hbase\_demo-1.0-snapshot-shaded.jar|实例引用的包名称，多个Package使用逗号（,）分隔，未引用为空。 |
|PlanJson|String|\{a:b\}|执行计划 |
|ProjectName|String|project1|项目名称 |
|Properties|String|\#checkpoint模式：EXACTLY\_ONCE 或者 AT\_LEAST\_ONCE\\n\#blink.checkpoint.mode=EXACTLY\_ONCE\\n\\n\#checkpoint间隔时间，单位毫秒\\n\#blink.checkpoint.interval.ms=180000\\n\\n\#rocksdb的数据生命周期，单位毫秒\\n\#state.backend.rocksdb.ttl.ms=129600000\\n\\n\#启用项目参数\\n\#enable.project.config=true\\nblink.job.timeZone=Asia/Shanghai|作业运行参数 |
|QueueName|String|root.project1|队列名称 |
|StartTime|Long|1599794335000|开始时间 |
|Starter|String|239960377092765296|启动人 |
|RequestId|String|B2F5A5C5-5CCC-45C8-B884-7152CC345EFA|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1/instances/123
```

正常返回示例

`XML` 格式

```
<RequestId>9B7BD33D-5F3A-4C4C-BCF5-AD686CAD2370</RequestId>
<Instance>
    <ExpectState>RUNNING</ExpectState>
    <EngineVersion>blink-3.4.4</EngineVersion>
    <ProjectName>project1</ProjectName>
    <ClusterId>rcmp9x37ztfb63g1x7lt****</ClusterId>
    <PlanJson/>
    <JobName>job1</JobName>
    <StartTime>1599794335000</StartTime>
    <Properties>#checkpoint模式：EXACTLY_ONCE 或者 AT_LEAST_ONCE
#blink.checkpoint.mode=EXACTLY_ONCE

#checkpoint间隔时间，单位毫秒
#blink.checkpoint.interval.ms=180000

#rocksdb的数据生命周期，单位毫秒
#state.backend.rocksdb.ttl.ms=129600000

#启用项目参数
#enable.project.config=true
blink.job.timeZone=Asia/Shanghai</Properties>
    <Starter>239960377092765296</Starter>
    <InputDelay>-99999999</InputDelay>
    <Code>--完整主类名，必填，例如com.alibaba.realtimecompute.DatastreamExample
blink.main.class=Hbase_Demo.HbaseDemo

--包含完整主类名的JAR包资源名称，多个JAR包时必填，例如blink_datastream.jar
--blink.main.jar=hbase_demo-1.0-snapshot.jar 

--默认state backend配置，当作业代码没有显式声明时生效
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

--默认Checkpoint配置，当作业代码没有显式声明时生效
blink.checkpoint.interval.ms=180000

--默认启用项目参数
--enable.project.config=true</Code>
    <ActualState>RUNNING</ActualState>
    <EngineJobHandler>application_1597654819348_0011|405a6d40d7f4f2618ed532359887d344</EngineJobHandler>
    <LastOperator>239960377092765296</LastOperator>
    <JobType>FLINK_STREAM</JobType>
    <Packages>hbase_demo-1.0-snapshot-shaded.jar</Packages>
    <AutoScaleParams>{"isOnOff":false,"strategyConfigure":null,"maxCU":null}</AutoScaleParams>
    <ApiType>DATASTREAM</ApiType>
    <Id>412536</Id>
    <LastOperateTime>1599794334000</LastOperateTime>
    <QueueName>root.project1</QueueName>
</Instance>
```

`JSON` 格式

```
{
	"RequestId": "9B7BD33D-5F3A-4C4C-BCF5-AD686CAD2370",
	"Instance": {
		"ExpectState": "RUNNING",
		"EngineVersion": "blink-3.4.4",
		"ProjectName": "project1",
		"ClusterId": "rcmp9x37ztfb63g1x7lt****",
		"PlanJson": "",
		"JobName": "job1",
		"StartTime": 1599794335000,
		"Properties": "#checkpoint模式：EXACTLY_ONCE 或者 AT_LEAST_ONCE\n#blink.checkpoint.mode=EXACTLY_ONCE\n\n#checkpoint间隔时间，单位毫秒\n#blink.checkpoint.interval.ms=180000\n\n#rocksdb的数据生命周期，单位毫秒\n#state.backend.rocksdb.ttl.ms=129600000\n\n#启用项目参数\n#enable.project.config=true\nblink.job.timeZone=Asia/Shanghai",
		"Starter": "239960377092765296",
		"InputDelay": -99999999,
		"Code": "--完整主类名，必填，例如com.alibaba.realtimecompute.DatastreamExample\nblink.main.class=Hbase_Demo.HbaseDemo\n\n--包含完整主类名的JAR包资源名称，多个JAR包时必填，例如blink_datastream.jar\n--blink.main.jar=hbase_demo-1.0-snapshot.jar \n\n--默认state backend配置，当作业代码没有显式声明时生效\nstate.backend.type=niagara\nstate.backend.niagara.ttl.ms=129600000\n\n--默认Checkpoint配置，当作业代码没有显式声明时生效\nblink.checkpoint.interval.ms=180000\n\n--默认启用项目参数\n--enable.project.config=true",
		"ActualState": "RUNNING",
		"EngineJobHandler": "application_1597654819348_0011|405a6d40d7f4f2618ed532359887d344",
		"LastOperator": "239960377092765296",
		"JobType": "FLINK_STREAM",
		"Packages": "hbase_demo-1.0-snapshot-shaded.jar",
		"AutoScaleParams": "{\"isOnOff\":false,\"strategyConfigure\":null,\"maxCU\":null}",
		"ApiType": "DATASTREAM",
		"Id": 412536,
		"LastOperateTime": 1599794334000,
		"QueueName": "root.project1"
	}
}
```

