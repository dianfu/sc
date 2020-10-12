# CreateProject

您可以通过CreateProject OpenAPI在集群中创建项目。

**说明：** 不能通过子账号的AK信息以openAPI方式创建项目。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=CreateProject&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
POST /api/v2/projects HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|deployType|String|是|public|集群类型：

 -   独享集群：cell
-   共享集群：public |
|name|String|否|project1|项目名称 |
|orderId|String|否|stream-abcd|共享模式实例ID

 **说明：** 仅共享模式需要填写。 |
|clusterId|String|否|cmy99ugusuco66x9qc6k\*\*\*\*|集群ID |
|managerIds|String|否|1234567|主账号ID |
|description|String|否|测试项目|项目描述 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|B661F03F-0F9D-4EFF-A40F-4ED1519A549A|请求ID |

## 示例

请求示例

```
/api/v2/projects
{\"name\":\"project1\",\"orderId\":\"stream-abcd\",\"clusterId\":\"cmy99ugusuco66x9qc6k****\",\"managerIds\":\"1234567\",\"description\":\"测试项目\",\"deployType\":\"cell\"}
```

正常返回示例

`XML` 格式

```
<requestId>B661F03F-0F9D-4EFF-A40F-4ED1519A549A</requestId>
```

`JSON` 格式

```
{"requestId":"B661F03F-0F9D-4EFF-A40F-4ED1519A549A"}
```

