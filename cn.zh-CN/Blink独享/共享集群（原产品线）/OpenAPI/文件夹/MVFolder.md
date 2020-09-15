# MVFolder

您可以通过MVFolder OpenAPI移动指定项目下的文件夹。移动非空文件夹时，会将文件夹下的作业一起移动。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=MVFolder&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
PUT /api/v2/projects/[projectName]/folders HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|destPath|String|是|/path2|目标路径 |
|projectName|String|是|project1|项目名称 |
|srcPath|String|是|/path1|原路径 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|D34CBA74-DED8-4B1E-80B4-DE4C58291726|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/folders
{\"srcPath\":\"/path1\",\"destPath\":\"/path2\"}
```

正常返回示例

`XML` 格式

```
<RequestId>D34CBA74-DED8-4B1E-80B4-DE4C58291726</RequestId>
```

`JSON` 格式

```
{
	"RequestId": "D34CBA74-DED8-4B1E-80B4-DE4C58291726"
}
```

