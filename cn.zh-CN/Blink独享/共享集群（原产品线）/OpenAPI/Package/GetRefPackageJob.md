# GetRefPackageJob

您可以通过GetRefPackageJob OpenAPI获取指定Package的所有作业详情。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetRefPackageJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/packages/[packageName]/jobs HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|packageName|String|是|package1.jar|Package名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |
|pageSize|Integer|否|50|包大小 |
|pageIndex|Integer|否|123|索引 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Jobs|Array of Job| |作业详情 |
|Job| | | |
|ApiType|String|DATASTREAM|API类型：DATASTREAM或SQL |
|ClusterId|String|et2cloud\*\*\*\*|集群ID |
|Code|String|blink.main.class=com.hjc.test.blink\_test.ChinanTopSpeedWindowing2\\r\\nblink.job.name=datastream\_prestest|作业代码 |
|CreateTime|Long|1551161842000|作业创建时间 |
|Creator|String|1709064687573327|作业创建者 |
|Description|String|test|作业备注 |
|EngineVersion|String|blink\_2.2.4|引擎版本 |
|FolderId|Long|10148|文件夹ID |
|IsCommitted|Boolean|true|是否上线 |
|JobId|String|9t9m6jfw8rb44iexhbpv3tnz|作业ID |
|JobName|String|job1|作业名称 |
|JobType|String|FLINK\_STREAM|作业类型：

 -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业 |
|Modifier|String|1709064687573327|最新修改者 |
|ModifyTime|Long|1548397575000|最新修改时间 |
|Packages|String|blink\_test\_datastream.jar|Package名称，多个Package请使用逗号（,）分隔。 |
|PlanJson|String|\{\\n \\"nodes\\": \[\\n \{\\n \\"id\\": 1,\\n \\"type\\": \\"Source: Custom Source\\",\\n \\"pact\\": \\"Data Source\\",\\n \\"contents\\": \\"Source: Custom Source\\",\\n \\"parallelism\\": 1\\n \},\\n \{\\n \\"id\\": 2,\\n \\"type\\": \\"Timestamps/Watermarks\\",\\n \\"pact\\": \\"Operator\\",\\n \\"contents\\": \\"Timestamps/Watermarks\\",\\n \\"parallelism\\": 1,\\n \\"predecessors\\": \[\\n \{\\n \\"id\\": 1,\\n \\"ship\_strategy\\": \\"FORWARD\\",\\n \\"side\\": \\"second\\"\\n \}\\n \]\\n \},\\n \{\\n \\"id\\": 4,\\n \\"type\\": \\"TriggerWindow\(GlobalWindows\(\), ListStateDescriptor\{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67\}, DeltaTrigger\(com.hjc.test.blink\_test.ChinanTopSpeedWindowing$1@350aac89, 50.0\), TimeEvictor\(10000\), WindowedStream.reduce\(WindowedStream.java:258\)\)\\",\\n \\"pact\\": \\"Operator\\",\\n \\"contents\\": \\"TriggerWindow\(GlobalWindows\(\), ListStateDescriptor\{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67\}, DeltaTrigger\(com.hjc.test.blink\_test.ChinanTopSpeedWindowing$1@350aac89, 50.0\), TimeEvictor\(10000\), WindowedStream.reduce\(WindowedStream.java:258\)\)\\",\\n \\"parallelism\\": 1,\\n \\"predecessors\\": \[\\n \{\\n \\"id\\": 2,\\n \\"ship\_strategy\\": \\"KEY-GROUP\\",\\n \\"side\\": \\"second\\"\\n \}\\n \]\\n \},\\n \{\\n \\"id\\": 5,\\n \\"type\\": \\"Sink: Unnamed\\",\\n \\"pact\\": \\"Data Sink\\",\\n \\"contents\\": \\"Sink: Unnamed\\",\\n \\"parallelism\\": 1,\\n \\"predecessors\\": \[\\n \{\\n \\"id\\": 4,\\n \\"ship\_strategy\\": \\"FORWARD\\",\\n \\"side\\": \\"second\\"\\n \}\\n \]\\n \}\\n \]\\n\}|作业上线的执行计划 |
|ProjectName|String|project1|项目名称 |
|Properties|String|\#checkpoint模式：EXACTLY\_ONCE 或者 AT\_LEAST\_ONCE\\n\#blink.checkpoint.mode=EXACTLY\_ONCE\\n\\n\#checkpoint间隔时间，单位毫秒\\n\#blink.checkpoint.interval.ms=180000\\n\\n\#rocksdb的数据生命周期，单位毫秒\\n\#state.backend.rocksdb.ttl.ms=129600000\\n|作业运行参数配置 |
|QueueName|String|root.bayes\_team|队列名称 |
|PageIndex|Integer|1|返回第几页 |
|PageSize|Integer|10|每页包含的作业数 |
|RequestId|String|31FDAE27-F135-4DAB-9C75-FADD6B0C4D1D|请求ID |
|TotalCount|Long|1|作业总数 |
|TotalPage|Integer|1|页码总数 |

## 示例

请求示例

```
/api/v2/projects/project1/packages/package1.jar/jobs
```

正常返回示例

`XML` 格式

```
<TotalCount>1</TotalCount>
<TotalPage>1</TotalPage>
<PageSize>10</PageSize>
<RequestId>31FDAE27-F135-4DAB-9C75-FADD6B0C4D1D</RequestId>
<Jobs>
    <Job>
        <ModifyTime>1583722910000</ModifyTime>
        <Description/>
        <EngineVersion>blink-3.4.4</EngineVersion>
        <ProjectName>bayes_team</ProjectName>
        <ClusterId>et2cloud****</ClusterId>
        <PlanJson>{
  "nodes": [
    {
      "id": 1,
      "type": "Source: Custom Source",
      "pact": "Data Source",
      "contents": "Source: Custom Source",
      "parallelism": 1
    },
    {
      "id": 2,
      "type": "Timestamps/Watermarks",
      "pact": "Operator",
      "contents": "Timestamps/Watermarks",
      "parallelism": 1,
      "predecessors": [
        {
          "id": 1,
          "ship_strategy": "FORWARD",
          "side": "second"
        }
      ]
    },
    {
      "id": 4,
      "type": "TriggerWindow(GlobalWindows(), ListStateDescriptor{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67}, DeltaTrigger(com.hjc.test.blink_test.ChinanTopSpeedWindowing$1@350aac89, 50.0), TimeEvictor(10000), WindowedStream.reduce(WindowedStream.java:258))",
      "pact": "Operator",
      "contents": "TriggerWindow(GlobalWindows(), ListStateDescriptor{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67}, DeltaTrigger(com.hjc.test.blink_test.ChinanTopSpeedWindowing$1@350aac89, 50.0), TimeEvictor(10000), WindowedStream.reduce(WindowedStream.java:258))",
      "parallelism": 1,
      "predecessors": [
        {
          "id": 2,
          "ship_strategy": "KEY-GROUP",
          "side": "second"
        }
      ]
    },
    {
      "id": 5,
      "type": "Sink: Unnamed",
      "pact": "Data Sink",
      "contents": "Sink: Unnamed",
      "parallelism": 1,
      "predecessors": [
        {
          "id": 4,
          "ship_strategy": "FORWARD",
          "side": "second"
        }
      ]
    }
  ]
}</PlanJson>
        <CreateTime>1551161842000</CreateTime>
        <JobName>job1</JobName>
        <Creator>1709064687573327</Creator>
        <FolderId>10148</FolderId>
        <Properties>#checkpoint模式：EXACTLY_ONCE 或者 AT_LEAST_ONCE
#blink.checkpoint.mode=EXACTLY_ONCE

#checkpoint间隔时间，单位毫秒
#blink.checkpoint.interval.ms=180000

#rocksdb的数据生命周期，单位毫秒
#state.backend.rocksdb.ttl.ms=129600000
</Properties>
        <Code>blink.main.class=com.hjc.test.blink_test.ChinanTopSpeedWindowing2
blink.job.name=datastream_prestest</Code>
        <JobType>FLINK_STREAM</JobType>
        <Packages>blink_test_datastream.jar</Packages>
        <ApiType>DATASTREAM</ApiType>
        <IsCommitted>true</IsCommitted>
        <Modifier>1709064687573327</Modifier>
        <QueueName>root.bayes_team</QueueName>
        <JobId>9t9m6jfw8rb44iexhbpv3tnz</JobId>
    </Job>
</Jobs>
<PageIndex>1</PageIndex>
```

`JSON` 格式

```
{
	"TotalCount": 1,
	"TotalPage": 1,
	"PageSize": 10,
	"RequestId": "31FDAE27-F135-4DAB-9C75-FADD6B0C4D1D",
	"Jobs": {
		"Job": [
			{
				"ModifyTime": 1583722910000,
				"Description": "",
				"EngineVersion": "blink-3.4.4",
				"ProjectName": "bayes_team",
				"ClusterId": "et2cloud****",
				"PlanJson": "{\n  \"nodes\": [\n    {\n      \"id\": 1,\n      \"type\": \"Source: Custom Source\",\n      \"pact\": \"Data Source\",\n      \"contents\": \"Source: Custom Source\",\n      \"parallelism\": 1\n    },\n    {\n      \"id\": 2,\n      \"type\": \"Timestamps/Watermarks\",\n      \"pact\": \"Operator\",\n      \"contents\": \"Timestamps/Watermarks\",\n      \"parallelism\": 1,\n      \"predecessors\": [\n        {\n          \"id\": 1,\n          \"ship_strategy\": \"FORWARD\",\n          \"side\": \"second\"\n        }\n      ]\n    },\n    {\n      \"id\": 4,\n      \"type\": \"TriggerWindow(GlobalWindows(), ListStateDescriptor{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67}, DeltaTrigger(com.hjc.test.blink_test.ChinanTopSpeedWindowing$1@350aac89, 50.0), TimeEvictor(10000), WindowedStream.reduce(WindowedStream.java:258))\",\n      \"pact\": \"Operator\",\n      \"contents\": \"TriggerWindow(GlobalWindows(), ListStateDescriptor{serializer=org.apache.flink.api.common.typeutils.base.ListSerializer@b2107f67}, DeltaTrigger(com.hjc.test.blink_test.ChinanTopSpeedWindowing$1@350aac89, 50.0), TimeEvictor(10000), WindowedStream.reduce(WindowedStream.java:258))\",\n      \"parallelism\": 1,\n      \"predecessors\": [\n        {\n          \"id\": 2,\n          \"ship_strategy\": \"KEY-GROUP\",\n          \"side\": \"second\"\n        }\n      ]\n    },\n    {\n      \"id\": 5,\n      \"type\": \"Sink: Unnamed\",\n      \"pact\": \"Data Sink\",\n      \"contents\": \"Sink: Unnamed\",\n      \"parallelism\": 1,\n      \"predecessors\": [\n        {\n          \"id\": 4,\n          \"ship_strategy\": \"FORWARD\",\n          \"side\": \"second\"\n        }\n      ]\n    }\n  ]\n}",
				"CreateTime": 1551161842000,
				"JobName": "job1",
				"Creator": "1709064687573327",
				"FolderId": 10148,
				"Properties": "#checkpoint模式：EXACTLY_ONCE 或者 AT_LEAST_ONCE\n#blink.checkpoint.mode=EXACTLY_ONCE\n\n#checkpoint间隔时间，单位毫秒\n#blink.checkpoint.interval.ms=180000\n\n#rocksdb的数据生命周期，单位毫秒\n#state.backend.rocksdb.ttl.ms=129600000\n",
				"Code": "blink.main.class=com.hjc.test.blink_test.ChinanTopSpeedWindowing2\r\nblink.job.name=datastream_prestest",
				"JobType": "FLINK_STREAM",
				"Packages": "blink_test_datastream.jar",
				"ApiType": "DATASTREAM",
				"IsCommitted": true,
				"Modifier": "1709064687573327",
				"QueueName": "root.bayes_team",
				"JobId": "9t9m6jfw8rb44iexhbpv3tnz"
			}
		]
	},
	"PageIndex": 1
}
```

