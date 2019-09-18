# BatchGetInstanceRunSummary {#doc_api_foas_BatchGetInstanceRunSummary .reference}

根据作业类型和名称获BatchGetInstanceRunSummary（批量获取实例运行信息）接口用来批量获取某个项目下，某一类作业（批作业或者流作业）的多个Job的运行实例情况。

## 调试 {#api_explorer .section}

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=BatchGetInstanceRunSummary&type=ROA&version=2018-11-11)

## 请求头 {#requestHeadList .section}

除公共请求头，该接口还需要指定以下请求头。关于公共请求头，请参见公共请求参数文档。

|名称|类型|是否必选|示例|描述|
|--|--|----|--|--|
|RegionId|String|否|cn-hangzhou-pre|集团内用户使用：

 -   公共云预发：cn-hangzhou-pre
-   集团内生产：cn-hangzhou-internal
-   集团内预发：cn-hangzhou-internal-pre

 |

## 请求语法 {#requestGrammer .section}

```
GET /api/v2/projects/[projectName]/runsummary HTTP/1.1
```

## 请求参数 {#parameters .section}

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|jobNames|String|是|job1,job2|作业名称，逗号（,）分隔。

 |
|jobType|String|是|FLINK\_STREAM|作业类型，批作业使用FLINK\_BATCH；流作业使用FLINK\_STREAM。

 |
|projectName|String|是|test\_project|项目名称。

 |

## 返回数据 {#resultMapping .section}

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|FD0FF8C0-779A-45EB-9674-FF3E127B10D2|请求ID。

 |
|RunSummarys| | |作业运行状态详情。

 |
|RunSummary| | |作业运行状态详情。

 |
|ActualState|String|RUNNING|返回作业的实际运行状态：

 -   WAITING：等待
-   RUNNING：正在运行
-   PAUSED：暂停
-   TERMINATED：停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（批作业）

 |
|EngineJobHandler|String|aplication\_x | xxxxx|YARN中为作业分配的名称：ApplicationID | JobID（JM分配的ID）。

 |
|ExpectState|String|RUNNIBG|作业期望状态：

 -   WAITING：等待
-   RUNNING：正在运行
-   PAUSED：暂停
-   TERMINATED：停止
-   SUCCESS：成功（批作业）
-   FAILED：失败（批作业）

 |
|Id|Long|1|运行实例ID。

 |
|InputDelay|Long|300|业务延时。

 |
|JobName|String|job1，job2|作业名称，多个逗号（,）分隔。

 |
|LastErrorMessage|String|error|上次错误信息。

 |
|LastErrorTime|Long|1548397575000|上次出现错误时间（时间戳，精确到毫秒）。

 |

## 示例 {#demo .section}

请求示例

``` {#request_demo}
GET /api/v2/projects/[projectName]/runsummary HTTP/1.1
RegionId: cn-hangzhou-pre
公共请求头
{
  "jobNames": "job1,job2"
  "jobType": "FLINK_STREAM"
  "projectName": "test_project"
}
```

正常返回示例

`XML` 格式

``` {#xml_return_success_demo}
<BatchGetInstanceRunSummaryResponse>
	  <RunSummarys>
		    <RunSummary>
			      <ExpectState>RUNNING</ExpectState>
			      <InputDelay>0</InputDelay>
			      <JobName>test</JobName>
			      <EngineJobHandler>application_1564648959240_0125|6150954a90852814ce0c6eace9868691</EngineJobHandler>
			      <ActualState>RUNNING</ActualState>
			      <Id>177474</Id>
		    </RunSummary>
	  </RunSummarys>
	  <RequestId>45BE0E07-8543-4EEE-BC4D-E846E4C1230D</RequestId>
</BatchGetInstanceRunSummaryResponse>
```

`JSON` 格式

``` {#json_return_success_demo}
{
	"RunSummarys":{
		"RunSummary":[
			{
				"ExpectState":"RUNNING",
				"InputDelay":0,
				"JobName":"test",
				"EngineJobHandler":"application_1564648959240_0125|6150954a90852814ce0c6eace9868691",
				"Id":177474,
				"ActualState":"RUNNING"
			}
		]
	},
	"RequestId":"45BE0E07-8543-4EEE-BC4D-E846E4C1230D"
}
```

## 错误码 { .section}

访问[错误中心](https://error-center.aliyun.com/status/product/foas)查看更多错误码。

