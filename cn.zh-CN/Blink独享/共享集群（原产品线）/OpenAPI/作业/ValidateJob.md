# ValidateJob

您可以通过ValidateJob OpenAPI对作业进行语法检查。如果检查通过，则返回参数。如果检查不通过，则会返回解析SQL出现错误的报错信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=ValidateJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/projects/[projectName]/jobs/[jobName]/validate HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|jobName|String|Path|是|job1|作业名称 |
|projectName|String|Path|是|project1|项目名称 |
|RegionId|String|Header|否|cn-hangzhou|区域ID |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|JobInOut|Struct| |作业输入输出 |
|Dims|Array of Dim| |作业中的维表 |
|Dim| | | |
|Alias|String|phoneNumber|维表别名 |
|Name|String|phoneNumber|表名 |
|Properties|Map|k:v|配置组 |
|Type|String|rds|表类型 |
|Workspace|String|project1|表所属项目 |
|Inputs|Array of Input| |输入数组详情 |
|Input| | | |
|Alias|String|datahub\_input1|源表别名 |
|Name|String|datahub\_input1|表名 |
|Properties|Map|k:v|配置组 |
|Type|String|datahub|表类型 |
|Workspace|String|project1|表所属项目 |
|Outputs|Array of Output| |返回数组详情 |
|Output| | | |
|Alias|String|result\_infor|结果表别名 |
|Name|String|result\_infor|表名 |
|Properties|Map|k:v|配置组 |
|Type|String|rds|表类型 |
|Workspace|String|project1|表所属项目 |
|RequestId|String|5A3EB3CC-D899-4B0B-AF1F-1C0C0B6A0F91|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1/validate
```

正常返回示例

`XML` 格式

```
<JobInOut>
    <Outputs>
        <Output>
            <Type>rds</Type>
            <Alias>result_infor</Alias>
            <Properties>
                <type>rds</type>
            </Properties>
            <Workspace/>
        </Output>
    </Outputs>
    <Dims>
        <Dim>
            <Type>ots</Type>
            <Alias>phoneNumber</Alias>
            <Properties>
                <type>ots</type>
            </Properties>
        </Dim>
    </Dims>
    <Inputs>
        <Input>
            <Type>datahub</Type>
            <Alias>datahub_input1</Alias>
            <Properties>
                <type>datahub</type>
            </Properties>
        </Input>
    </Inputs>
</JobInOut>
<RequestId>5A3EB3CC-D899-4B0B-AF1F-1C0C0B6A0F91</RequestId>
```

`JSON` 格式

```
{
	"JobInOut": {
		"Outputs": {
			"Output": [
				{
					"Type": "rds",
					"Alias": "result_infor",
					"Properties": {
						"type": "rds"
					},
					"Workspace": ""
				}
			]
		},
		"Dims": {
			"Dim": [
				{
					"Type": "ots",
					"Alias": "phoneNumber",
					"Properties": {
						"type": "ots"
					}
				}
			]
		},
		"Inputs": {
			"Input": [
				{
					"Type": "datahub",
					"Alias": "datahub_input1",
					"Properties": {
						"type": "datahub"
					}
				}
			]
		}
	},
	"RequestId": "5A3EB3CC-D899-4B0B-AF1F-1C0C0B6A0F91"
}
```

