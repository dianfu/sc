# ListProject

您可以通过ListProject OpenAPI查询多个项目的信息。查询的项目信息可根据参数选择，进行自由组合。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=ListProject&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|RegionId|String|Header|否|cn-hangzhou|区域ID |
|name|String|Query|否|job1|项目名称 |
|deployType|String|Query|否|cell|集群类型：

 -   CELL：独享集群
-   PUBLIC：共享集群 |
|clusterId|String|Query|否|cmy99ugusuco66x9qc6k\*\*\*\*|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|region|String|Query|否|cn-hangzhou|区域ID |
|pageSize|Integer|Query|否|1|每页显示的项目数 |
|pageIndex|Integer|Query|否|2|查询的页码 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|PageIndex|Integer|1|查询的页码 |
|PageSize|Integer|10|每页显示的项目数 |
|Projects|Array of Project| |项目详情 |
|Project| | | |
|ClusterId|String|rcmp9x37ztfb63g1x7lt\*\*\*\*|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|CreateTime|Long|1597655424000|项目创建时间 |
|Creator|String|23996037709276\*\*\*\*|项目创建者的UID |
|DeployType|String|CELL|集群类型：

 -   CELL：独享集群
-   PUBLIC：共享集群 |
|Description|String|test|项目描述 |
|Id|String|e8edisrtou71a2neroi5f3kc|请求ID |
|ManagerIds|String|1079926896999421|管理员ID列表。多个ID请使用逗号（,）分隔。 |
|Modifier|String|27395716024545\*\*\*\*|项目修改者UID |
|ModifyTime|Long|1605610424000|项目修改时间 |
|Name|String|project1|项目名称 |
|Region|String|cn-hangzhou|区域ID |
|State|String|ENABLE|项目状态 |
|RequestId|String|5A0EA261-AA2F-47FF-B7D9-A785ED1D0ADE|请求ID |
|TotalCount|Long|3|总项目数 |
|TotalPage|Integer|1|总页码数 |

## 示例

请求示例

```
/api/v2/projects
```

正常返回示例

`XML` 格式

```
<TotalCount>3</TotalCount>
<TotalPage>1</TotalPage>
<PageSize>10</PageSize>
<RequestId>5A0EA261-AA2F-47FF-B7D9-A785ED1D0ADE</RequestId>
<Projects>
    <Project>
        <DeployType>CELL</DeployType>
        <ModifyTime>1597655424000</ModifyTime>
        <ManagerIds>1079926896999421</ManagerIds>
        <Description>test</Description>
        <ClusterId>rcmp9x37ztfb63g1x7lt****</ClusterId>
        <State>ENABLE</State>
        <CreateTime>1597655424000</CreateTime>
        <Region>cn-hangzhou</Region>
        <Creator>23996037709276****</Creator>
        <Id>7ht9ed87rtd48bof9mu8895z</Id>
        <Modifier>23996037709276****</Modifier>
        <Name>project1</Name>
    </Project>
    <Project>
        <DeployType>CELL</DeployType>
        <ModifyTime>1605610424000</ModifyTime>
        <ManagerIds>1079926896999421</ManagerIds>
        <Description>stream4demo</Description>
        <ClusterId>rulpqd442sgxbtw9736r****</ClusterId>
        <State>ENABLE</State>
        <CreateTime>1605610424000</CreateTime>
        <Region>cn-beijing</Region>
        <Creator>27395716024545****</Creator>
        <Id>e8edisrtou71a2neroi5f3kc</Id>
        <Modifier>27395716024545****</Modifier>
        <Name>stream4demo</Name>
    </Project>
</Projects>
<PageIndex>1</PageIndex>
```

`JSON` 格式

```
{
	"TotalCount": 3,
	"TotalPage": 1,
	"PageSize": 10,
	"RequestId": "5A0EA261-AA2F-47FF-B7D9-A785ED1D0ADE",
	"Projects": {
		"Project": [
			{
				"DeployType": "CELL",
				"ModifyTime": 1597655424000,
				"ManagerIds": "1079926896999421",
				"Description": "test",
				"ClusterId": "rcmp9x37ztfb63g1x7lt****",
				"State": "ENABLE",
				"CreateTime": 1597655424000,
				"Region": "cn-hangzhou",
				"Creator": "23996037709276****",
				"Id": "7ht9ed87rtd48bof9mu8895z",
				"Modifier": "23996037709276****",
				"Name": "project1"
			},
			{
				"DeployType": "CELL",
				"ModifyTime": 1605610424000,
				"ManagerIds": "1079926896999421",
				"Description": "stream4demo",
				"ClusterId": "rulpqd442sgxbtw9736r****",
				"State": "ENABLE",
				"CreateTime": 1605610424000,
				"Region": "cn-beijing",
				"Creator": "27395716024545****",
				"Id": "e8edisrtou71a2neroi5f3kc",
				"Modifier": "27395716024545****",
				"Name": "stream4demo"
			}
		]
	},
	"PageIndex": 1
}
```

