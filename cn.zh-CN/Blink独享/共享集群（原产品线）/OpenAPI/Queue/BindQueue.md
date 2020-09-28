# BindQueue

您可以通过BindQueue OpenAPI将Project绑定在已经存在但没有被其他Project绑定的Queue上，绑定成功后，系统才会给您的Project分配资源，您才能在该项目上创建和运行作业。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=BindQueue&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
POST /api/v2/projects/[projectName]/queue HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|clusterId|String|是|d6wxwo5tnrmuamx2ly3m\*\*\*\*|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|projectName|String|是|project1|项目名称 |
|queueName|String|是|queue1|要绑定的Queue名称

 **说明：** 您可以使用[GetClusterQueueInfo](~~117517~~)获取Queue名称信息。 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/queue
{\"clusterId\":\"d6wxwo5tnrmuamx2ly3m****\",\"queueName\":\"queue1\"}
```

正常返回示例

`XML` 格式

```
<requestId>C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2</requestId>
```

`JSON` 格式

```
{"requestId":"C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2"}
```

