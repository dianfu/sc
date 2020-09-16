# UpdatePackage

您可以通过UpdatePackage OpenAPI更新开发控制台上保存在指定项目下的Package。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=UpdatePackage&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
PUT /api/v2/projects/[projectName]/packages/[packageName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|packageName|String|是|package1.jar|Package名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |
|originName|String|否|package2.jar|Package别名 |
|description|String|否|test|Package备注描述 |
|md5|String|否|3F7468C153E529B141C326332DF15D05|Package的Md5值 |
|ossEndpoint|String|否|oss-cn-hangzhou-internal.aliyuncs.com|OSS接入点 |
|ossBucket|String|否|blinktest2.oss-cn-hangzhou-internal.aliyuncs.com|OSS Bucket |
|ossOwner|String|否|owner1|OSS所有者 |
|ossPath|String|否|path1/path2/a.jar|OSS路径 |
|tag|String|否|\{"function":"group by time"\}|Package的标记 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|77D5CB98-5E9F-42BB-8AA9-001125B48E28|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/packages/package1.jar
```

正常返回示例

`XML` 格式

```
<RequestId>77D5CB98-5E9F-42BB-8AA9-001125B48E28</RequestId>
```

`JSON` 格式

```
{
	"RequestId": "77D5CB98-5E9F-42BB-8AA9-001125B48E28"
}
```

