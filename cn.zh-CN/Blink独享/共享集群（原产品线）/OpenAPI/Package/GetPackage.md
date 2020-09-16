# GetPackage

您可以通过GetPackage OpenAPI获取Package详细信息。

**说明：** GetPackage只能获取到通过WEB或CreatePackage接口在开发控制台上保存的Package相关信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetPackage&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/packages/[packageName] HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|packageName|String|是|aliyun-java-sdk-foas-2.9.0.jar|Package名称 |
|projectName|String|是|project1|项目名称 |
|RegionId|String|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Package|Struct| |Package详情 |
|CreateTime|Long|1600156643000|创建时间（13位时间戳，精确到毫秒）。 |
|Creator|String|1709064687573327|创建者 |
|Description|String|adas|Package备注 |
|Md5|String|e0899e291f0524c6e6572c126599f73b|Package的Md5值 |
|Modifier|String|1709064687573327|最新修改者 |
|ModifyTime|Long|1600156643000|最新修改时间 |
|OriginName|String|aliyun-java-sdk-foas-2.9.0.jar|Package别名 |
|OssBucket|String|blinktest2.oss-cn-hangzhou-internal.aliyuncs.com|OSS Bucket |
|OssEndpoint|String|oss-cn-hangzhou-internal.aliyuncs.com|OSS接入点 |
|OssOwner|String|owner1|OSS所有者 |
|OssPath|String|79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes|OSS路径 |
|PackageName|String|package1.jar|Package名称 |
|ProjectName|String|project1|项目名称 |
|Tag|String|\{"function":"group by time"\}|Package的标记 |
|Type|String|JAR|Package类型：

 -   JAR：JAR包
-   DICTIONARY：普通文件
-   SCRIPT：脚本
-   PYTHON：Python文件或者ZIP包 |
|RequestId|String|7076AAD2-EF55-479A-867E-3245B9105E1D|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/packages/aliyun-java-sdk-foas-2.9.0.jar
```

正常返回示例

`XML` 格式

```
<RequestId>7076AAD2-EF55-479A-867E-3245B9105E1D</RequestId>
<Package>
    <ModifyTime>1600156643000</ModifyTime>
    <Description>adas</Description>
    <ProjectName>project1</ProjectName>
    <PackageName>aliyun-java-sdk-foas-2.9.0.jar</PackageName>
    <CreateTime>1600156643000</CreateTime>
    <Creator>1709064687573327</Creator>
    <OriginName>aliyun-java-sdk-foas-2.9.0.jar</OriginName>
    <Type>JAR</Type>
    <OssPath>79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes</OssPath>
    <Modifier>1709064687573327</Modifier>
    <Md5>e0899e291f0524c6e6572c126599f73b</Md5>
</Package>
```

`JSON` 格式

```
{
	"RequestId": "7076AAD2-EF55-479A-867E-3245B9105E1D",
	"Package": {
		"ModifyTime": 1600156643000,
		"Description": "adas",
		"ProjectName": "project1",
		"PackageName": "aliyun-java-sdk-foas-2.9.0.jar",
		"CreateTime": 1600156643000,
		"Creator": "1709064687573327",
		"OriginName": "aliyun-java-sdk-foas-2.9.0.jar",
		"Type": "JAR",
		"OssPath": "79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes",
		"Modifier": "1709064687573327",
		"Md5": "e0899e291f0524c6e6572c126599f73b"
	}
}
```

