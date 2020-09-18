# DeleteJob

您可以通过DeleteJob OpenAPI删除已经下线的作业，删除未下线作业会报错。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=DeleteJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
DELETE /api/v2/projects/[projectName]/jobs/[jobName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|jobName|String|是|job1|作业名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|78261AC6-B5C9-4514-B82F-39BCC45A5D63|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1
```

正常返回示例

`XML` 格式

```
<RequestId>78261AC6-B5C9-4514-B82F-39BCC45A5D63</RequestId>
```

`JSON` 格式

```
{
	"RequestId": "78261AC6-B5C9-4514-B82F-39BCC45A5D63"
}
```

