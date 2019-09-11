# CreateQueue {#concept_227253 .concept}

CreateQueue OpenAPI将集群资源分配到创建的Queue中，供绑定了这个Queue的项目使用。

**说明：** 

-   CreateQueue仅适用于独享模式。
-   Queue创建成功后即开始消耗资源。

## CreateQueue请求参数 {#section_m0i_s4i_nwc .section}

|参数|类型|是否必选|示例值|描述|
|clusterId|String|是|d6wxwo5tnrmuamx2ly3m\*\*\*\*|集群ID。|
|gpu|Integer|否|1|GPU处理硬件块数。|
|maxMemMB|Integer|是|1024|Queue拥有的最大Memory。|
|maxVcore|Integer|是|100|Queue拥有的最大CPU。|
|queueName|String|是|root.default|Queue的名称。|

## CreateQueue返回参数 {#section_8h5_ns9_b2h .section}

|参数|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|FD0FF8C0-779A-45EB-9674-FF3E127B1\*\*\*\*|请求ID|

## 请求示例 {#section_nor_y4u_x59 .section}

``` {#codeblock_ndy_wcz_to8}
http(s)://[Endpoint]/?clusterId=cmy99ugusuco66x9qc6k****
&RegionId=cn-shanghai
&<公共请求参数>
```

