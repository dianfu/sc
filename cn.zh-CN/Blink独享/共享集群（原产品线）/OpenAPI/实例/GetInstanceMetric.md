# GetInstanceMetric

您可以通过GetInstanceMetric获取正在运行实例的所有Metric信息。

使用GetInstanceMetric调用未运行的实例的Metric时，会产生报错。`metricJson`参数的详细请参见[opentsdb标准协议](http://opentsdb.net/docs/build/html/api_http/query/index.html?highlight=query)。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetInstanceMetric&type=ROA&version=2018-11-11)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /api/v2/projects/[projectName]/jobs/[jobName]/metric HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|jobName|String|Path|是|job1|作业名称 |
|metricJson|String|Body|是|\{"start":1606291390000,"end":1606294626216,"limit":"avg:sample:50","queries":\[\{"downsample":"20s-avg","metric":"blink.projectName.jobName.task\_failover.rate","granularity":"20s","aggregator":"max"\}\]\}|使用特定JSON来获取Metric：

 -   start：Metric开始时间。使用13位时间戳，精确到毫秒。
-   limit：取各条线值类型：max、avg和min。取值：bottom、above和sample。
-   end：Metric结束时间。使用13位时间戳，精确到毫秒。
-   downsample：采样方式时间（秒）。取值：max、avg和min。
-   metric：blink.项目名称.作业名称.指标名称。常用指标名称例如：业务延时（delay）。
-   granularity：采样时间，每隔多少秒取一个点。需要根据start和end的进行计算。采集时间点太多会造成调用超时。
-   aggregator：聚合方式。按照采样时间将底层的点聚合后输出为一个点。聚合方式有最大值max，最小值min和平均值avg。 |
|projectName|String|Path|是|project1|项目名称 |
|RegionId|String|Header|否|cn-hangzhou|地域ID

 **说明：** GetInstanceMetric |
|instanceId|Long|Query|否|-1|InstanceID。流作业和批作业对应的实例ID如下：

 -   流作业：只有一个运行实例，此处填`-1L`，指在线上运行的。
-   批作业：可以通过`ListInstance`接口或者`Startjob`接口获得。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Metrics|Array of Metric| |Metric信息详情 |
|Metric| | | |
|Dps|Map|k:v|时间点和对应的Metric值 |
|MetricName|String|delay|Metric名称 |
|Summary|Float|10.2|聚合后的Metric值 |
|Tags|Map|k:v|Metric标记 |
|RequestId|String|FD0FF8C0-779A-45EB-9674-FF3E127B10D2|请求ID |

## Metric名称对应表

|参数名称

|指标名称 |
|------|------|
|业务延时（ms）

|`delay`|
|数据滞留时间（ms）

|`fetched_delay`|
|数据间隔时间（ms）

|`no_data_delay`|
|作业失败率

|`task_failover.rate`|
|Source的tps数据输入

|`tps.rate`|
|Sink的数据输出

|`sink.outTps.rate`|
|Source的RPS数据输出

|`parserTps.rate`|
|Source的数据流量输入（byte）

|`inBps.rate`|
|Source的脏数据比例

|`parserSkipCount`|
|Checkpoint Duration（ms）

|`lastCheckpointDuration`|
|Checkpoint大小（byte）

|`lastCheckpointSize`|
|checkpoint对齐时间（ns）

|`checkpointAlignmentTime`|
|checkpoint数量

|`checkpoints`|
|获取state的时间（ns）

|`rocksdb_get.mean`|
|写入state的时间（ns）

|`rocksdb_put.mean`|
|seek（ns）

|`niagara_seek.meam / rocksdb_seek.mean`|
|state大小（byte）

|`state.state_size / state.state_newsize`|
|GMS GC Time（ms）

|`Status.JVM.GarbageCollector.ConcurrentMarkSweep.Time`|
|GMS GC Rate

|`Status.JVM.GarbageCollector.ConcurrentMarkSweep.Count`|
|WaterMark Delay（ms）

|`watermarkLatency`|
|数据迟到丢弃TPS

|`lateRecordsDroppedRate`|
|数据迟到累计丢弃数

|`numLateRecordsDropped`|
|读TT延时（ms）

|`input.tt.readLatency`|
|Task Input TPS

|`numRecordsInPerSecond.rate`|
|Task Output TPS

|`numRecordsOutPerSecond.rate`|
|Input Queue Usage

|`buffers.inPoolUsage`|
|Output Queue Usage

|`buffers.outPoolUsage`|
|Time Used In Processing Per Second（ns）

|`taskLatency.sum`|
|Time Used In Waiting Oputput Per Second（ns）

|`waitOutput.sum`|
|TaskLatency Histogram Mean（ns）

|`taskLatency.histogram.mean`|
|WaitOutput Histogram Mean（ns）

|`waitOutput.histogram.mean`|
|WaitInput Histogram Mean（ns）

|`waitInput.histogram.mean`|
|PartitionLatency Mean（ns）

|`partitionLatency.mean`|
|Process MEM Rss（kb）

|`Status.JVM.Memory.Process.rss`|
|CPU Usage

|`Status.JVM.CPU.Usage`|
|Memory Heap Used（byte）

|`Status.JVM.Memory.Heap.Used`|
|Memory NonHeap Used（byte）

|`Status.JVM.Memory.NonHeap.Used`|
|Threads Count

|`Status.JVM.Threads.Count`|
|GC\(CMS\)

|`Status.JVM.GarbageCollector.ConcurrentMarkSweep.Count`|

## 示例

请求示例

```
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<requestId>4D7AB7CF-4EF2-4635-9137-51803CD71DAD</requestId>
<metrics>
    <metricName>blink.bayes_team.ss2.delay</metricName>
    <dps>
        <1574247000>4.929E+8</1574247000>
        <1574247600>4.9350016E+8</1574247600>
        <1574248200>494100352</1574248200>
        <1574248800>494700544</1574248800>
        <1574249400>495300736</1574249400>
        <1574250000>495900896</1574250000>
    </dps>
    <summary>494400448</summary>
    <tags/>
</metrics>
```

`JSON` 格式

```
{
    "requestId":"4D7AB7CF-4EF2-4635-9137-51803CD71DAD",
    "metrics":[
        {
            "metricName":"blink.bayes_team.ss2.delay",
            "dps":{
                "1574247000":"4.929E+8",
                "1574247600":"4.9350016E+8",
                "1574248200":"494100352",
                "1574248800":"494700544",
                "1574249400":"495300736",
                "1574250000":"495900896"
            },
            "summary":494400448,
            "tags":{}
        }
    ]
}
```

