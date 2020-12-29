# CreateJob

您可以通过CreateJob OpenAPI在指定项目下创建作业。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=CreateJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
POST /api/v2/projects/[projectName]/jobs HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|apiType|String|Body|是|Datastream|API类型：

 -   DATASTREAM：Datastream作业
-   SQL：SQL作业 |
|clusterId|String|Body|是|rcmp9x37ztfb63g1x7lt\*\*\*\*|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|code|String|Body|是|blink.main.class=com.hjc.test.blink\_test.ChinanTopSpeedWindowing2\\r\\nblink.job.name=datastream\_prestest|作业代码：

 -   SQL作业：填写SQL代码。
-   DataStream作业：填写DataStream作业的相关参数。 |
|engineVersion|String|Body|是|blink\_2.2.4|引擎版本 |
|jobName|String|Body|是|job1|作业名称 |
|jobType|String|Body|是|FLINK\_STREAM|作业类型：

 -   FLINK\_STREAM：流作业
-   FLINK\_BATCH：批作业 |
|planJson|String|Body|是|\{a:b\}|执行计划 |
|projectName|String|Path|是|project1|项目名称 |
|queueName|String|Body|是|root.default|队列名称 |
|RegionId|String|Header|是|区域ID|区域ID

 **说明：** 公共云用户请忽略此参数。 |
|properties|String|Body|否|\{k:v\}|作业运行的相关参数 |
|packages|String|Body|否|package1,package2|JAR包名称，多个使用逗号（,）分隔。 |
|description|String|Body|否|test|作业备注 |
|folderId|Long|Body|否|123|文件夹ID |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|B661F03F-0F9D-4EFF-A40F-4ED1519A549A| |

## 示例

请求示例

```
/api/v2/projects/project1/jobs
{\"jobName\":\"job1\",\"jobType\":\"FLINK_STREAM\",\"apiType\":\"Datastream\",\"code\":\"blink.main.class=com.hjc.test.blink_test.ChinanTopSpeedWindowing2\r\nblink.job.name=datastream_prestest\",\"planJson\":\"{a:b}\",\"engineVersion\":\"blink_2.2.4\",\"clusterId\":\"rcmp9x37ztfb63g1x7lt****\",\"queueName\":\"root.default\"}
```

正常返回示例

`XML` 格式

```
<requestId>B661F03F-0F9D-4EFF-A40F-4ED1519A549A</requestId>
```

`JSON` 格式

```
{
    "requestId": "B661F03F-0F9D-4EFF-A40F-4ED1519A549A"
}
```

