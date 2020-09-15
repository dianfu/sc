# DeleteProject

您可以通过DeleteProject OpenAPI删除集群中的项目。如果需要删除的项目已经和Queue绑定，也会将项目和Queue解绑（不会删除Queue）。为了防止资源浪费，请及时将Queue绑定新的项目。

**说明：** DeleteProject仅支持独享模式。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=DeleteProject&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
DELETE /api/v2/projects/[projectName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|projectName|String|是|project1|项目名称 |
|RegionId|String|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|3F4ADBCF-B116-4680-80B3-E9A139841A0D|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1
```

正常返回示例

`XML` 格式

```
<requestId>3F4ADBCF-B116-4680-80B3-E9A139841A0D</requestId>
```

`JSON` 格式

```
{"requestId":"3F4ADBCF-B116-4680-80B3-E9A139841A0D"}
```

