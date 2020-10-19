# GetRawPlanJson

GetRawPlanJson API可以获取作业初始的执行计划。

## GetRawPlanJson请求参数

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|job1|作业名称|
|projectName|String|是|project1|项目名称|
|autoconfEnable|Boolean|否|true|是否开启智能调优，开启或根据作业历史metric，生成一份执行计划；不开启则使用默认执行计划，默认开启|
|expectedCore|Float|否|1|期望cpu数|
|expectedGB|Float|否|4|期望内存|

## GetRawPlanJson返回参数

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|SessionId|String|`xxxxxx`|会话ID，需要传入checkrowplanjson接口中配合使用。|

## GetRawPlanJson请求示例

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

