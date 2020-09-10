# GetJob

您可以通过GetJob获取作业详细信息。

********

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/jobs/[jobName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|jobName|String|是|job1|作业名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Job|Struct| |作业详情 |
|ApiType|String|DATASTREAM|作业类型： DATASTREAM或SQL。 |
|ClusterId|String|xxx|集群ID |
|Code|String|xxxxx|Instance的运行代码：

 -   SQL作业返回SQL代码。
-   Datastream作业返回Datastream配置。 |
|CreateTime|Long|1595493794000|作业创建时间 |
|Creator|String|xxx|作业创建者 |
|Description|String|test|作业备注信息 |
|EngineVersion|String|blink-3.5.0-hotfix|引擎版本 |
|FileId|String|75724|文件ID |
|FolderId|Long|26808|文件夹ID |
|IsCommitted|Boolean|true|是否为上线状态。 |
|JobId|String|rd8r1lgccsnnchh9fqwukw6e|作业ID |
|JobName|String|datastream\_enable|作业名称 |
|JobType|String|FLINK\_STREAM|作业类型：

 -   FLINK\_STREAM：流作业。
-   FLINK\_BATCH：批作业。 |
|Modifier|String|xxx|最新修改者 |
|ModifyTime|Long|1597743765000|最新修改时间 |
|Packages|String|big-data-1.0-snapshot.jar|实例引用的包名称，多个包使用逗号（,）分隔，未引用为空。 |
|PlanJson|String|\{a:b\}|作业上线的执行计划 |
|ProjectName|String|bayes\_team|项目名称 |
|Properties|String|k:v|作业运行配置参数 |
|QueueName|String|root.default|队列名称 |
|RequestId|String|2C6E2F4C-2FA1-4159-87DA-676DB15F4826|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1
```

正常返回示例

`XML` 格式

```
<requestId>2C6E2F4C-2FA1-4159-87DA-676DB15F4826</requestId>
<job>
    <jobName>datastream_enable</jobName>
    <projectName>bayes_team</projectName>
    <jobType>FLINK_STREAM</jobType>
    <apiType>DATASTREAM</apiType>
    <code>xxxxx</code>
    <packages>big-data-1.0-snapshot.jar</packages>
    <isCommitted>true</isCommitted>
    <creator>xxx</creator>
    <createTime>1595493794000</createTime>
    <modifier>xxx</modifier>
    <modifyTime>1597743765000</modifyTime>
    <engineVersion>blink-3.5.0-hotfix</engineVersion>
    <clusterId>xxx</clusterId>
    <queueName>root.default</queueName>
    <folderId>26808</folderId>
    <jobId>rd8r1lgccsnnchh9fqwukw6e</jobId>
    <fileId>75724</fileId>
</job>
```

`JSON` 格式

```
{"requestId":"2C6E2F4C-2FA1-4159-87DA-676DB15F4826","job":{"jobName":"datastream_enable","projectName":"bayes_team","jobType":"FLINK_STREAM","apiType":"DATASTREAM","code":"xxxxx","packages":"big-data-1.0-snapshot.jar","isCommitted":true,"creator":"xxx","createTime":1595493794000,"modifier":"xxx","modifyTime":1597743765000,"engineVersion":"blink-3.5.0-hotfix","clusterId":"xxx","queueName":"root.default","folderId":26808,"jobId":"rd8r1lgccsnnchh9fqwukw6e","fileId":"75724"}}
```

