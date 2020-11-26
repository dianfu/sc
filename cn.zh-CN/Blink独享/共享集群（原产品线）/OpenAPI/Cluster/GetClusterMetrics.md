# GetClusterMetrics

您可以通过GetClusterMetrics获取集群的CPU、存储或YARN的资源分配状况等信息。仅独享模式支持GetClusterMetrics接口。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetClusterMetrics&type=ROA&version=2018-11-11)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /api/v2/clusters/[clusterId]/metrics HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|clusterId|String|Path|是|yourClusterID|集群ID。<note\>不是集群的名称。</note\> |
|metricJson|String|Body|是|\{ "end":1606291550061, "limit":"avg:sample:50", "queries":\[ \{ "aggregator":"avg", "downsample":"avg", "granularity":"60s", "metric":"ClusterID.system.cpu.user" \} \], "start":1606291390000 \}|查询Metric提交的JSON信息。 |
|RegionId|String|Header|是|cn-hangzhou|地域ID |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Metrics|Array of Metric| |Metric信息。 |
|Metric| | | |
|Dps|Map|k:v|时间点和对应的Metric值。 |
|MetricName|String|delay|Metric名称。 |
|Summary|Float|10.2|聚合后的Metric值。 |
|Tags|Map|k:v|Metric标记。 |
|RequestId|String|FD0FF8C0-779A-45EB-9674-FF3E127B10D2|请求ID。 |

## 示例

请求示例

```
POST /api/v2/clusters/[clusterId]/metrics HTTP/1.1
RegionId: cn-hangzhou
公共请求头
{
  "clusterId": "<yourClusterID>"
  "metricJson": "{metric: "clusterid.system.mem.memtotal", aggregator: "avg", downsample: "avg"}"
}
```

正常返回示例

`XML` 格式

```
<RequestId>A017B343-33DC-4A97-BAC5-4B57872D5B61</RequestId>
<Metrics>
    <metricName>et2hunbu.yarn.resourcemanager.jvmmetrics.MemHeapUsed</metricName>
    <dps>
        <1574244620>3675.839599609375</1574244620>
        <1574244640>3817.712646484375</1574244640>
        <1574244660>3864.84130859375</1574244660>
        <1574244680>3973.963134765625</1574244680>
        <1574244700>4121.57958984375</1574244700>
        <1574244720>4170.6123046875</1574244720>
        <1574244740>4279.62109375</1574244740>
        <1574244760>4427.05615234375</1574244760>
        <1574244780>4472.11474609375</1574244780>
    </dps>
    <summary>6700.7467978583445</summary>
    <tags/>
</Metrics>
```

`JSON` 格式

```
{
    "RequestId":"A017B343-33DC-4A97-BAC5-4B57872D5B61",
    "Metrics":[
        {
            "metricName":"et2hunbu.yarn.resourcemanager.jvmmetrics.MemHeapUsed",
            "dps":{
                "1574244620":3675.839599609375,
                "1574244640":3817.712646484375,
                "1574244660":3864.84130859375,
                "1574244680":3973.963134765625,
                "1574244700":4121.57958984375,
                "1574244720":4170.6123046875,
                "1574244740":4279.62109375,
                "1574244760":4427.05615234375,
                "1574244780":4472.11474609375
            },
            "summary":6700.7467978583445,
            "tags":{
            }
        }
    ]
}
```

