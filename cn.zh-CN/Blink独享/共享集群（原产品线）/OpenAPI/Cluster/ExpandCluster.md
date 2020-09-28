# ExpandCluster

您可以通过ExpandCluster OpenAPI对已有集群进行Slave机型的扩容。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=ExpandCluster&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
PUT /api/v2/clusters/[clusterId]/expand HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|clusterId|String|是|cmy99ugusuco66x9qc6k\*\*\*\*|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|RegionId|String|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |
|model|String|否|Ecs\_4c16g|机器型号

 **说明：** 扩容机型必须和已有Slave机型一致。 |
|count|Integer|否|5|扩容后的机器台数 |
|userVSwitch|String|否|vsw-abcd|交换机名称

 **说明：** 扩容提示vSwitch不足时，需要填写该参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2|请求ID |

## 示例

请求示例

```
/api/v2/clusters/cmy99ugusuco66x9qc6k****/expand
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

