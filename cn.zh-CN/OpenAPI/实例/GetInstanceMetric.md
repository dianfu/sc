# GetInstanceMetric {#concept_ifv_tb1_rgb .concept}

通过GetInstanceMetric API，您可以可以获取foas上某个运行实例的所有metric信息。

**说明：** GetInstanceMetric API可以同时获取多个metric信息。metricjson参数详情请参见[opentsdb标准协议](http://opentsdb.net/docs/build/html/api_http/query/index.html?highlight=query)。

## GetInstanceMetric请求参数 {#section_nd5_qd1_rgb .section}

|参数|类型|是否必选|示例值|描述|
|jobName|String|是|`job1`|作业名称。|
|metricJson|String|是| ``` {#codeblock_42e_jlo_ckj .language-json}
{
    "start":1547637620000,
    "limit":"avg:sample:50",
    "end":1547638420000,
    "queries":[
        {
            "downsample":"20s-avg",
            "metric":"blink.bayes_team.huayuan_test_job.delay",
            "granularity":"20s",
            "aggregator":"max"
        },
        {
            "downsample":"20s-avg",
            "metric":"blink.bayes_team.huayuan_test_job.fetched_delay",
            "granularity":"20s",
            "aggregator":"max"
        }
    ]
}
```

 |使用特定json来获取metric： -   start：metric开始时间（使用13位时间戳，精确到毫秒）。
-   limit：`[取各条线值类型：max,avg,min]:[取值方式:bottom,above,sample]:[数目]`。
-   end：metric结束时间（使用13位 时间戳，精确到毫秒）。
-   downsample：下采样方式 `[时间(秒)]-[取值方式：max,avg,min]`。
-   metric：`blink.[项目名称].[作业名称].[指标名称]`。常用指标名称如下表所示 ：
    -   granularity：采样时间（每隔多少秒取一个点，需要根据start和end时长来取，点数太多会造成调用超时）。
    -   aggregator：聚合方式（按照采样时间将底层的点聚合后输出为一个点，聚合方式有最大值max，最小值min，平均值avg三种）。

 |
|projectName|String|是|`project1`|项目名称。|
|instanceId|Long|否|`-1`|InstanceID，流式作业只有一个运行实例，此处填`-1L`，即在线上运行的，批作业可以通过ListInstance、Startjob等接口获得。|

## GetInstanceMetric返回参数 {#section_lww_xd1_rgb .section}

|参数|类型|示例值|描述|
|RequestId|String|`FD0FF8C0-779A-45EB-9674-FF3E127B10D2`|请求ID，方便foas定位问题。|
|Metrics.MetricName|String|`delay`|metric名称。|
|Metrics.Dps|Map|`k:v`|时间点和对应的metric值。|
|Metrics.Summary|Float|`10.2`|聚合后的metric值。|
|Metrics.Tags|Map|`k:v`|metric标记。|

## metric名称对应表 {#section_uwg_zd1_rgb .section}

|参数名称|指标名称|
|业务延时（ms）|delay|
|数据间隔延时（ms）|fetched\_delay|
|数据等待延时（ms）|no\_data\_delay|
|作业失败率|task\_failover.rate|
|Source的tps数据输入|tps.rate|
|Sink的数据输出|sink.outTps.rate|
|Source的RPS数据输出|parserTps.rate|
|Source的数据流量输入（byte）|inBps.rate|
|Source的脏数据比例|parserSkipCount|
|Checkpoint Duration（ms）|lastCheckpointDuration|
|Checkpoint大小（byte）|lastCheckpointSize|
|checkpoint对齐时间（ns）|checkpointAlignmentTime|
|checkpoint数量|checkpoints|
|获取state的时间（ns）|rocksdb\_get.mean|
|写入state的时间（ns）|rocksdb\_put.mean|
|seek（ns）|niagara\_seek.meam / rocksdb\_seek.mean|
|state大小（byte）|state.state\_size / state.state\_newsize|
|GMS GC Time（ms）|Status.JVM.GarbageCollector.ConcurrentMarkSweep.Time|
|GMS GC Rate|Status.JVM.GarbageCollector.ConcurrentMarkSweep.Count|
|WaterMark Delay（ms）|watermarkLatency|
|数据迟到丢弃TPS|lateRecordsDroppedRate|
|数据迟到累计丢弃数|numLateRecordsDropped|
|读TT延时（ms）|input.tt.readLatency|
|Task Input TPS|numRecordsInPerSecond.rate|
|Task Output TPS|numRecordsOutPerSecond.rate|
|Input Queue Usage|buffers.inPoolUsage|
|Output Queue Usage|buffers.outPoolUsage|
|Time Used In Processing Per Second（ns）|taskLatency.sum|
|Time Used In Waiting Oputput Per Second（ns）|waitOutput.sum|
|TaskLatency Histogram Mean（ns）|taskLatency.histogram.mean|
|WaitOutput Histogram Mean（ns）|waitOutput.histogram.mean|
|WaitInput Histogram Mean（ns）|waitInput.histogram.mean|
|PartitionLatency Mean（ns）|partitionLatency.mean|
|Process MEM Rss（kb）|Status.JVM.Memory.Process.rss|
|CPU Usage|Status.JVM.CPU.Usage|
|Memory Heap Used（byte）|Status.JVM.Memory.Heap.Used|
|Memory NonHeap Used（byte）|Status.JVM.Memory.NonHeap.Used|
|Threads Count|Status.JVM.Threads.Count|
|GC\(CMS\)|Status.JVM.GarbageCollector.ConcurrentMarkSweep.Count|

## GetInstanceMetric请求示例 {#section_gwg_121_rgb .section}

``` {#codeblock_h50_l3t_y54}
http(s)://[Endpoint]/?Action=undefined
&<公共请求参数>
```

