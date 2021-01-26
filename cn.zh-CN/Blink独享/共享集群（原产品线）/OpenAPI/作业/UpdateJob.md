# UpdateJob

您可以通过UpdateJob OpenAPI更新作业属性。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=UpdateJob&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
PUT /api/v2/projects/[projectName]/jobs/[jobName] HTTP/1.1
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|jobName|String|Path|是|job1|作业名称 |
|projectName|String|Path|是|project1|项目名称 |
|packages|String|Body|否|package1.jar|package名称 |
|planJson|String|Body|否|\{a:b\}|作业执行计划 |
|code|String|Body|否|code|作业代码 |
|RegionId|String|Header|否|cn-hangzhou|区域ID |
|description|String|Body|否|test|作业备注 |
|engineVersion|String|Body|否|blink\_2.2.4|引擎版本 |
|clusterId|String|Body|否|d6wxwo5tnrmuamx2ly3m7vkz|集群ID

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|queueName|String|Body|否|root.default|队列名称 |
|folderId|Long|Body|否|123|文件夹ID |
|properties|String|Body|否|blink.checkpoint.interval.ms:180000|作业运行配置参数，参数详情请参见配置参数表。 |

## 配置参数

|参数名称

|说明

|备注 |
|------|----|----|
|blink.checkpoint.mode

|Checkpoint模式

|AT\_LEAST\_ONCE：至少计算一次。EXACTLY\_ONCE：精确计算一次。 |
|blink.checkpoint.interval.ms

|Checkpoint间隔时间

|数据类型为LONG，例如180000，单位为毫秒。 |
|blink.checkpoint.timeout.ms

|Checkpoint超时时间

|数据类型为LONG，例如600000，单位为毫秒，默认值为10分钟。 |
|blink.job.option.jmMemMB

|Job Manager内存

|例如1024 MB。 |
|blink.job.option

|传给flink命令run action的参数，可以传递多个参数，具体请参见Flink命令说明。

|例如设置Task Manager的JVM线程的堆栈大小，可以配置：blink.job.option=-yD env.java.opts='-Xss8192K'。 |
|client.jvm.option

|编译阶段的JVM

|例如：-Xss2048k。 |
|blink.miniBatch.allowLatencyMs

|Batch最大允许延迟时间

|添加或删除该参数Job状态会丢失，修改值大小状态不会丢失，数据类型为LONG， 例如10000，单位为毫秒。 |
|blink.miniBatch.size

|batch最大条数

|添加或删除该参数Job状态会丢失，修改值大小状态不会丢失；此参数不能设置过大，如果一个Batch实际去完重的数据超过65536，在访问statebackend时可能触发JNI OOM异常；数据类型为LONG， 例如1000。 |
|blink.microBatch.allowLatencyMs

|Batch最大允许延迟时间，与minibatch的区别在于microbatch是基于batchmark触发的，而不是timer，具有更高的效率。

|数据类型为LONG， 例如10000，Blink 2.2及以上版本支持该参数，单位为毫秒。 |
|blink.localAgg.enabled

|是否开启Local-Global Agg。需要开启MiniBatch，Local-Global Agg功能才生效。

|添加或删除该参数Job状态会丢失，修改值大小状态不会丢失；Blink 2.2版本默认值为true，其他版本默认值为false。 |
|blink.partialAgg.enabled

|是否开启Partial-Final Agg。Parital-Final Agg常用于解决COUNT DISTINCT热点问题。

|默认值为false。Blink 2.2.0及以上版本支持该参数。 |
|sql.exec.mini-batch.window.enabled

|是否开启minibatch window

|默认值为false（不开启），Blink 3.2.0及以上版本支持该参数。 |
|sql.exec.source.idle.timeout.ms

|Source空闲超时的时间，当source被标记为空闲后，下游就不会再等待该分区source发来的watermark，从而能触发window。

|默认值为-1（不开启），取值大于0则表示开启。Blink 3.2.0及以上版本支持该参数。 |
|blink.state.ttl.ms

|TopN和groupBy节点的状态过期清理时间。状态在指定时间内没有被更新过则会被清除。

|数据类型为LONG， 例如60000，单位为毫秒，默认为不清除。 |
|blink.topn.cache.size

|TopN缓存数据的条数。例如Top100，配置缓存10000条，即缓存了100组数据。

|数据类型为LONG，默认值为10000。 |
|blink.job.submit.timeoutInSeconds

|SQL编译超时时间，建议设置最大不超过250s。

|数据类型为LONG，默认值为180。 |
|blink.job.timeZone

|作业的时区

|默认为Bayes前端时区，例如Asia/Shanghai。 |
|blink.object.reuse

|是否开启对象重用。如果不开启，chain一起的operator之前的数据传输会发生序列化或反序列化

|Blink 2.2.0及以上版本默认值为true，其他版本默认值为false。 |
|blink.auto.watermark.interval.ms

|Watermark发送的频率。需要权衡延迟和性能。太高会导致延迟增加，太低会导致频繁发送watermark，影响性能。

|默认值为100ms。 |
|blink.job.timestamp.reserve.ms

|对于Ads、Petadata、SLS、Elasticsearch、DataHub类型的Sink，在写入timestamp类型时支持毫秒精度。

|默认值为false，Blink 2.2.6及以上版本支持该参数。 |
|yarn.app.blink.source.buffer-len

|Connector source里面RecordReader线程作为生产者，上层ParallelReader线程作为消费者，中间的阻塞队列的长度，当数据源单条数据较小，数据量极大时，调高此项能提升部分性能。

|默认值为10。 |
|yarn.app.blink.source.source.idle-interval

|Connector source里面ParallelReader无数据可读时，sleep的时间。

|默认值为10，单位为100ms，Blink 2.2.5及以上版本支持该参数。 |
|sql.exec.fault.tolerance.enabled

|是否开启容错机制

|默认值为false，Blink 3.4.0及以上版本支持该参数。

| |
|state.backend.niagara.ttl.ms

|Niagara StateBackend中数据的保存时间

|数据类型为LONG，例如129600000，默认值为一天半。 |
|state.backend.block.cache.size.mb

|Niagara StateBackend读缓存大小

|默认值为256 MB。 |
|state.backend.mem.table.size.mb

|Niagara StateBackend写缓存大小

|默认值为128 MB。 |
|state.backend.gemini.ttl.ms

|Gemini StateBackend数据的保存时间

|数据类型为LONG，例如129600000，默认值为一天半。 |
|state.backend.gemini.mem.size.mb

|Gemini StateBackend Native内存大小

|默认值为384 MB。 |
|state.backend.gemini.ttl.ms

|Gemini StateBackend中数据的保存时间

|数据类型为LONG，例如129600000，默认值为一天半。 |
|state.backend.block.cache.size.mb

|Gemini StateBackend读缓存大小

|默认值为256 MB。 |
|state.backend.mem.table.size.mb

|Gemini StateBackend写缓存大小

|默认值为128 MB。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|C3FC248C-1CF4-4A64-97C1-0BD667A4D09D|请求ID |

## 示例

请求示例

```
/api/v2/projects/project1/jobs/job1
```

正常返回示例

`XML`格式

```
<RequestId>C3FC248C-1CF4-4A64-97C1-0BD667A4D09D</RequestId>
```

`JSON`格式

```
{
	"RequestId": "C3FC248C-1CF4-4A64-97C1-0BD667A4D09D"
}
```

