# CreateQueue

您可以使用CreateQueue将集群资源分配到创建的Queue中，并绑定Queue中的项目。Queue创建成功后即开始消耗资源。

**说明：** CreateQueue仅限独享模式使用。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=CreateQueue&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
POST /api/v2/clusters/[clusterId]/queue HTTP/1.1
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|clusterId|String|Path|是|cmy99ugusuco66x9qc6k\*\*\*\*|集群ID。

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|maxMemMB|Integer|Body|是|16|Queue的最大内存，单位为MB。 |
|maxVcore|Integer|Body|是|4|Queue的最大CPU个数。

 **说明：** 100Vcore = 1CU = 4G MEM。 |
|queueName|String|Body|是|root.default|Queue的名称。 |
|RegionId|String|Header|否|cn-shanghai|地域ID。 |
|gpu|Integer|Body|否|1|GPU个数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|9F1CAD8D-E80E-45AF-82D7-8314FE17A34A|请求ID |

## 示例

请求示例

```
/api/v2/clusters/[clusterId]/queue
{\"queueName\":\"root.default\",\"maxVcore\":4,\"maxMemMB\":16}
```

正常返回示例

`XML`格式

```
<RequestId>9F1CAD8D-E80E-45AF-82D7-8314FE17A34A</RequestId>
```

`JSON`格式

```
{
    "RequestId":"9F1CAD8D-E80E-45AF-82D7-8314FE17A34A"
}
```

