# DeletePackage

您可以通过DeletePackag OpenAPI删除开发控制台上通过CreatePackage创建的Package。删除后开发控制台无法再获取对应的Package信息。

**说明：** 使用DeletePackage删除不存在的Package时，系统会报错。如果已上线的作业已引用该Package，删除该Package时也会报错。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=DeletePackage&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
DELETE /api/v2/projects/[projectName]/packages/[packageName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|packageName|String|是|package.jar|Package名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|A604B9FA-D181-43D7-A064-5FF971B90466|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/packages/package.jar
```

正常返回示例

`XML` 格式

```
<RequestId>A604B9FA-D181-43D7-A064-5FF971B90466</RequestId>
```

`JSON` 格式

```
{
	"RequestId": "A604B9FA-D181-43D7-A064-5FF971B90466"
}
```

