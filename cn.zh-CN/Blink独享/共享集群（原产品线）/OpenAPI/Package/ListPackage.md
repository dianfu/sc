# ListPackage

您可以通过ListPackag OpenAPI获取指定Project下满足特定条件的Package信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=ListPackage&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/packages HTTPS
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|projectName|String|是|project1|项目名称 |
|packageName|String|否|package1.jar|Package名称 |
|type|String|否|JAR|Package类型：

 -   JAR：JAR包
-   DICTIONARY：普通文件
-   SCRIPT：脚本
-   PYTHON：Python文件或者ZIP包 |
|pageSize|Integer|否|10|每页包含Package数量 |
|pageIndex|Integer|否|1|索引 |
|tag|String|否|\{"function":"group by time"\}|Package的标记 |
|RegionId|String|否|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Packages|Array of Package| |Package详情 |
|Package| | | |
|CreateTime|Long|1600156643000|创建时间（13位时间戳，精确到毫秒） |
|Creator|String|1709064687573327|创建者 |
|Description|String|adas|Package备注 |
|Md5|String|e0899e291f0524c6e6572c126599f73b|Package的Md5值 |
|Modifier|String|1709064687573327|最新修改者 |
|ModifyTime|Long|1600156643000|最新修改时间 |
|OriginName|String|aliyun-java-sdk-foas-2.9.0.jar|Package别名 |
|OssBucket|String|79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes|OssBucket |
|OssEndpoint|String|oss-cn-hangzhou-internal.aliyuncs.com|Oss接入点 |
|OssOwner|String|owner1|Oss拥有者 |
|OssPath|String|path1/path2/a.jar|Oss路径 |
|PackageName|String|aliyun-java-sdk-foas-2.9.0.jar|Package名称 |
|ProjectName|String|project1|项目名称 |
|Tag|String|\{"function":"group by time"\}|Package的标记 |
|Type|String|JAR|Package 类型：

 -   JAR：JAR包
-   DICTIONARY：普通文件
-   SCRIPT：脚本
-   PYTHON：Python文件或者ZIP包 |
|PageIndex|Integer|1|第几页 |
|PageSize|Integer|10|每页包含Package数量 |
|RequestId|String|F33B93C0-56E6-43BD-B30F-E1FF1D4EF1EE|请求ID |
|TotalCount|Long|4|Package总数 |
|TotalPage|Integer|1|总页面数 |

## 示例

请求示例

```
/api/v2/projects/project1/packages
```

正常返回示例

`XML` 格式

```
<TotalCount>4</TotalCount>
<TotalPage>1</TotalPage>
<PageSize>10</PageSize>
<RequestId>F33B93C0-56E6-43BD-B30F-E1FF1D4EF1EE</RequestId>
<Packages>
    <Package>
        <ModifyTime>1600156643000</ModifyTime>
        <OriginName>aliyun-java-sdk-foas-2.9.0.jar</OriginName>
        <Type>JAR</Type>
        <Description>adas</Description>
        <ProjectName>project1</ProjectName>
        <PackageName>aliyun-java-sdk-foas-2.9.0.jar</PackageName>
        <CreateTime>1600156643000</CreateTime>
        <OssPath>79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes</OssPath>
        <Creator>1709064687573327</Creator>
        <Modifier>1709064687573327</Modifier>
        <Md5>e0899e291f0524c6e6572c126599f73b</Md5>
    </Package>
    <Package>
        <ModifyTime>1596436948000</ModifyTime>
        <OriginName>blink-udx-2.x-1.0-snapshot.jar</OriginName>
        <Type>JAR</Type>
        <Description>123</Description>
        <ProjectName>bayes_team</ProjectName>
        <PackageName>blink-udx-2.x-1.0-snapshot.jar</PackageName>
        <CreateTime>1596436947000</CreateTime>
        <OssPath>79zec7svps7hca0xnogbqpvu/blink-udx-2.x-1.0-snapshot.jar/dr6r901verct65f612ca4t0c.bayes</OssPath>
        <Creator>1709064687573327</Creator>
        <Modifier>1709064687573327</Modifier>
        <Md5>70bf694b7c737ee608f1aaf11b15816a</Md5>
    </Package>
    <Package>
        <ModifyTime>1551161104000</ModifyTime>
        <OriginName>blink_test_datastream.jar</OriginName>
        <Type>JAR</Type>
        <Description>1</Description>
        <ProjectName>bayes_team</ProjectName>
        <PackageName>blink_test_datastream.jar</PackageName>
        <CreateTime>1551161103000</CreateTime>
        <OssPath>79zec7svps7hca0xnogbqpvu/blink_test_datastream.jar/mg5174rvvsv472xrkvv23wn3.bayes</OssPath>
        <Creator>1709064687573327</Creator>
        <Modifier>1709064687573327</Modifier>
        <Md5>7fd9782e4aad6e4b6a9aed8521774d40</Md5>
    </Package>
    <Package>
        <ModifyTime>1546094734000</ModifyTime>
        <OriginName>sql_udx-3.0</OriginName>
        <Type>JAR</Type>
        <Description>1</Description>
        <ProjectName>bayes_team</ProjectName>
        <PackageName>sql_udx-3.0</PackageName>
        <CreateTime>1546094733000</CreateTime>
        <OssPath>79zec7svps7hca0xnogbqpvu/sql_udx-3.0/6za6dxizvsscdjpupnbp0cfq.bayes</OssPath>
        <Creator>1709064687573327</Creator>
        <Modifier>1709064687573327</Modifier>
        <Md5>d8e19321c1b33af2e52643ea169d4f7d</Md5>
    </Package>
</Packages>
<PageIndex>1</PageIndex>
```

`JSON` 格式

```
{
	"TotalCount": 4,
	"TotalPage": 1,
	"PageSize": 10,
	"RequestId": "F33B93C0-56E6-43BD-B30F-E1FF1D4EF1EE",
	"Packages": {
		"Package": [
			{
				"ModifyTime": 1600156643000,
				"OriginName": "aliyun-java-sdk-foas-2.9.0.jar",
				"Type": "JAR",
				"Description": "adas",
				"ProjectName": "project1",
				"PackageName": "aliyun-java-sdk-foas-2.9.0.jar",
				"CreateTime": 1600156643000,
				"OssPath": "79zec7svps7hca0xnogbqpvu/aliyun-java-sdk-foas-2.9.0.jar/l1jsjfzlps4z4pk5rhutb91k.bayes",
				"Creator": "1709064687573327",
				"Modifier": "1709064687573327",
				"Md5": "e0899e291f0524c6e6572c126599f73b"
			},
			{
				"ModifyTime": 1596436948000,
				"OriginName": "blink-udx-2.x-1.0-snapshot.jar",
				"Type": "JAR",
				"Description": "123",
				"ProjectName": "bayes_team",
				"PackageName": "blink-udx-2.x-1.0-snapshot.jar",
				"CreateTime": 1596436947000,
				"OssPath": "79zec7svps7hca0xnogbqpvu/blink-udx-2.x-1.0-snapshot.jar/dr6r901verct65f612ca4t0c.bayes",
				"Creator": "1709064687573327",
				"Modifier": "1709064687573327",
				"Md5": "70bf694b7c737ee608f1aaf11b15816a"
			},
			{
				"ModifyTime": 1551161104000,
				"OriginName": "blink_test_datastream.jar",
				"Type": "JAR",
				"Description": "1",
				"ProjectName": "bayes_team",
				"PackageName": "blink_test_datastream.jar",
				"CreateTime": 1551161103000,
				"OssPath": "79zec7svps7hca0xnogbqpvu/blink_test_datastream.jar/mg5174rvvsv472xrkvv23wn3.bayes",
				"Creator": "1709064687573327",
				"Modifier": "1709064687573327",
				"Md5": "7fd9782e4aad6e4b6a9aed8521774d40"
			},
			{
				"ModifyTime": 1546094734000,
				"OriginName": "sql_udx-3.0",
				"Type": "JAR",
				"Description": "1",
				"ProjectName": "bayes_team",
				"PackageName": "sql_udx-3.0",
				"CreateTime": 1546094733000,
				"OssPath": "79zec7svps7hca0xnogbqpvu/sql_udx-3.0/6za6dxizvsscdjpupnbp0cfq.bayes",
				"Creator": "1709064687573327",
				"Modifier": "1709064687573327",
				"Md5": "d8e19321c1b33af2e52643ea169d4f7d"
			}
		]
	},
	"PageIndex": 1
}
```

