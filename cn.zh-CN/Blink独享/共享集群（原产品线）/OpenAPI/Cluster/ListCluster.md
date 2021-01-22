# ListCluster

您可以通过ListCluster OpenAPI查询集群信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=ListCluster&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/clusters HTTP/1.1
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|RegionId|String|Header|否|cn-hangzhou|区域ID |
|clusterId|String|Query|否|rulpqd442sgxbtw9736r\*\*\*\*|集群ID |
|displayName|String|Query|否|demo|集群名称 |
|state|String|Query|否|RUNNING|集群状态：

 -   STARTING： 集群创建中
-   EXPANDING：集群扩容中（增加Slave机型台数）
-   UPGRADING ：集群变配中（提高或者降低Master）
-   DESTROYING ：集群注销中
-   DESTROYED ：集群已销毁
-   REDUCING：集群缩容中（减少Slave机型台数）
-   MAINTAINING：集群维护中 |
|region|String|Query|否|cn-beijing|集群所属地区 |
|pageSize|Integer|Query|否|10|每页显示的集群数 |
|pageIndex|Integer|Query|否|1|指定查询的页码 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Clusters|Array of Cluster| |集群详情 |
|Cluster| | | |
|ClusterId|String|rulpqd442sgxbtw9736r\*\*\*\*|集群ID |
|Description|String|demo|集群备注 |
|DisplayName|String|demo|集群名称 |
|GmtCreate|Long|1605609438000|集群VPC创建时间 |
|GmtModified|Long|1605610377000|集群VPC修改时间 |
|Operator|String|27395716024545\*\*\*\*|集群最后操作者UID |
|OwnerId|String|107992689699\*\*\*\*|集群拥有者UID |
|RegionId|String|cn-beijing|集群所属地区 |
|State|String|RUNNING|集群状态：

 -   STARTING： 集群创建中
-   EXPANDING：集群扩容中（增加Slave机型台数）
-   UPGRADING ：集群变配中（提高或者降低Master）
-   DESTROYING ：集群注销中
-   DESTROYED ：集群已销毁
-   REDUCING：集群缩容中（减少Slave机型台数）
-   MAINTAINING：集群维护中 |
|ZoneId|String|cn-beijing-e|可用地域ID |
|PageIndex|Integer|1|指定查询的页码 |
|PageSize|Integer|10|每页显示的集群数 |
|RequestId|String|10C4F123-351D-4D2B-94A4-D6FF456AEA6E|请求ID |
|TotalCount|Long|3|总集群数 |
|TotalPage|Integer|10|总页数 |

## 示例

请求示例

```
http(s)://[Endpoint]/?RegionId=cn-hangzhou&<公共请求参数>
```

正常返回示例

`XML`格式

```
<TotalCount>3</TotalCount>
<TotalPage>1</TotalPage>
<PageSize>10</PageSize>
<RequestId>10C4F123-351D-4D2B-94A4-D6FF456AEA6E</RequestId>
<Clusters>
    <Cluster>
        <Operator>27395716024545****</Operator>
        <GmtCreate>1605609438000</GmtCreate>
        <Description>demo</Description>
        <ZoneId>cn-beijing-e</ZoneId>
        <OwnerId>107992689699****</OwnerId>
        <ClusterId>rulpqd442sgxbtw9736r****</ClusterId>
        <State>RUNNING</State>
        <DisplayName>demo</DisplayName>
        <GmtModified>1605610377000</GmtModified>
        <RegionId>cn-beijing</RegionId>
    </Cluster>
    <Cluster>
        <Operator>23996037709276****</Operator>
        <GmtCreate>1597654146000</GmtCreate>
        <Description>test</Description>
        <ZoneId>cn-hangzhou-f</ZoneId>
        <OwnerId>107992689699****</OwnerId>
        <ClusterId>rcmp9x37ztfb63g1x7lt****</ClusterId>
        <State>RUNNING</State>
        <DisplayName>niuna</DisplayName>
        <GmtModified>1601051659000</GmtModified>
        <RegionId>cn-hangzhou</RegionId>
    </Cluster>
</Clusters>
<PageIndex>1</PageIndex>
```

`JSON`格式

```
{
	"TotalCount": 3,
	"TotalPage": 1,
	"PageSize": 10,
	"RequestId": "10C4F123-351D-4D2B-94A4-D6FF456AEA6E",
	"Clusters": {
		"Cluster": [
			{
				"Operator": "27395716024545****",
				"GmtCreate": 1605609438000,
				"Description": "demo",
				"ZoneId": "cn-beijing-e",
				"OwnerId": "107992689699****",
				"ClusterId": "rulpqd442sgxbtw9736r****",
				"State": "RUNNING",
				"DisplayName": "demo",
				"GmtModified": 1605610377000,
				"RegionId": "cn-beijing"
			},
			{
				"Operator": "23996037709276****",
				"GmtCreate": 1597654146000,
				"Description": "test",
				"ZoneId": "cn-hangzhou-f",
				"OwnerId": "107992689699****",
				"ClusterId": "rcmp9x37ztfb63g1x7lt****",
				"State": "RUNNING",
				"DisplayName": "niuna",
				"GmtModified": 1601051659000,
				"RegionId": "cn-hangzhou"
			}
		]
	},
	"PageIndex": 1
}
```

