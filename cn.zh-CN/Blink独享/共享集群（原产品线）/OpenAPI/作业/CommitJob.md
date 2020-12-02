# CommitJob

您可以通过CommitJob提交作业。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=CommitJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
PUT /api/v2/projects/[projectName]/jobs/[jobName]/commit HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|jobName|String|Path|是|job1|作业名称 |
|projectName|String|Path|是|project1|项目名称 |
|RegionId|String|Header|是|cn-hangzhou|区域ID

 **说明：** 公共云用户请忽略此参数。 |
|isOnOff|Boolean|Body|否|true|是否启用AutoScale。 |
|maxCU|Float|Body|否|10.0|最大CU数，作业运行期间使用的资源不会超过该上限。单位为CU，`1 CU=1 Core + 4 GB RAM`。 |
|configure|String|Body|否|\{"fetch\_delay":1.0,"recommendOnly":true\}|您可以配置以下几项：

 -   调优策略：系统根据选择的策略和期望值对作业自动调优，以达到期望值。目前只支持数据滞留时间fetch\_delay，单位为秒。
-   期望值：数据滞留时间阈值，当Source的数据滞留时间超过该阈值时，触发AutoScale并优化作业并发度。
-   建议recommendOnly：true表示开启PlanJson建议功能。

 **说明：**

-   仅支持通过AutoScale后台服务提供PlanJson建议。
-   仅在AutoScale设置为true时，recommendOnly生效。
-   仅Blink 3.7.8以上版本支持recommendOnly。
-   仅Blink 3.x版本支持AutoScale功能。
-   当开启AutoScale时，需要传入maxCu和configure参数。 |
|recommendOnly|Boolean|Body|否|false|启用AutoScale后，系统是否自动修改您的PlanJson：

 -   true：系统不会自动修改。
-   false（默认值）：系统会自动修改。

 **说明：** 可以通过SDK中getInstanceHistoryAutoScalePlanListRequest获取PlanJson列表。 |
|suspendPeriodParam.N.plan|String|Body|否|ORIGINAL|指定暂停作业后使用的PlanJson：

 -   ORIGINAL：初始的PlanJson。
-   CURRENT：当前的PlanJson。 |
|suspendPeriodParam.N.policy|String|Body|否|EVERY\_DAY\_TIME|指定暂停时间段：

 -   EVERY\_DAY\_TIME：指定每天的暂停时间段，需要填写暂停起始时间（startTime:HH:mm）和暂停结束时间（endTime:HH:mm）。
-   SPECIFIED\_TIME：指定具体的暂停时间段，需要填写暂停起始时间（startTime:yyyy-MM-dd HH:mm）和暂停结束时间（endTime:yyyy-MM-dd HH:mm）。 |
|suspendPeriodParam.N.startTime|String|Body|否|18:20|暂停时间段的开始时间。

 **说明：** 根据policy设置不同格式的时间格式。 |
|suspendPeriodParam.N.endTime|String|Body|否|18:25|暂停时间段的结束时间。

 **说明：** 根据policy设置不同格式的时间格式。 |
|suspendPeriods|String|Body|否|\[\{"endTime":"18:20","startTime":"18:00","plan":"ORIGINAL","policy":"EVERY\_DAY\_TIME"\},\{"endTime":"19:00","startTime":"18:40","plan":"CURRENT","policy":"EVERY\_DAY\_TIME"\},\{"endTime":"2020-07-17 20:00","startTime":"2020-07-17 19:30","plan":"CURRENT","policy":"SPECIFIED\_TIME"\}\]|开启AutoScale后，指定暂停AutoScale时间段和使用的PlanJson。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1/commit
```

正常返回示例

`XML` 格式

```
<requestId>C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2</requestId>
```

`JSON` 格式

```
{
    "requestId": "C5C6746D-2FFC-4D5D-A6A8-FA8DF7585DC2"
}
```

