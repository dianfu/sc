# IoT解决方案之多维度传感器数据分析

本文通过案例为您介绍如何使用实时计算完成多维度传感器数据分析。

## 背景说明

在经济全球化的浪潮中，工业制造商面临的竞争日趋激烈。汽车、航空、高新技术、食品与饮料、纺织和制药行业生产商需要进行创新，但更换现有基础设施是一项艰巨的任务。传统系统和设备已使用了几十年，维护费用高昂，但更换系统和设备成本巨大，而且还有生产放缓且质量得不到保障的风险。

同时，安全风险比以往更为严重，流程自动化的需求也更为紧迫。由于配备机械臂、装配线、包装机械等众多活动部件，远程应用必须能够无缝进行设备部署、更新、故障转移以及处理寿命终止事项。因此，制造业需要高可靠性和高可用性的系统，才能保障业务安全可靠地实时运行。

所有新一代系统必须能够捕捉、分析工业设施产生的巨量数据，并及时对这些数据做出响应。为了更好地发展，制造商需要进行优化升级。使用阿里云实时计算系统，搭配使用阿里云IoT套件，可以帮助用户实时分析和诊断工业设备的运行状况，实时检测运行故障，实时预测制品良率。

下面以一个工业制造设备使用实时计算分析大量工业传感器传入数据，实时进行数据清洗和归纳、实时监控设备关键指标、实时将数据清洗并写入在线OLAP系统为例，为您介绍如何使用实时计算完成多维度传感器数据分析。

## 业务描述

该工业客户拥有1千多台设备，分布在不同城市的多个厂区，每个设备上有10个不同种类传感器，这些传感器，大概每5秒采集并上传一份数据到日志服务（Log/SLS），每个采集点格式如下。

|s\_id|s\_value|s\_ts|
|-----|--------|-----|
|传感器ID|传感器当前值|发送时间|

同时，上述传感器分布在多个设备、多个厂区，用户在RDS上记录传感器、设备、厂区的分布维表，表格如下：

|s\_id|s\_type|device\_id|factory\_id|
|-----|-------|----------|-----------|
|传感器ID|传感器监控类型|设备ID|厂区ID|

上述信息存放在RDS上，用户希望传感器上传的数据能够和上述数据关联，并将传感器数据按照设备归类每1分钟打平为一张宽表，表格如下：

|ts|device\_id|factory\_id|device\_temp|device\_pres|
|--|----------|-----------|------------|------------|
|时间|设备ID|工厂ID|设备温度|设备压力|

为了简化不必要的逻辑，我们假定仅有两种类型的监控传感器，即温度和压力，以方便后续的计算，后续计算逻辑如下：

1.  筛选温度大于80的设备，并向下游触发告警。用户选择使用MQ作为消息触发源，也就是实时计算将温度大于80的设备过滤并投递给MQ，触发下游的用户定义的告警系统。
2.  将数据写出到在线OLAP系统中，这里用户选择了PetaData。下游用户开发一整套BI系统对接PetaData进行多维度展示。

整体架构如下：

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9079649951/p34745.png)

## 要点说明

-   如何打平为一张宽表？

    通常IoT的数据都是一个传感器上传一个维度的数据，这样的数据并不利于后续数据加工分析。使用阿里云实时计算可以将数据按照一定窗口汇聚，并根据传感器不同维度进行数据筛选，打平为一张宽表。

-   为什么要选择MQ作为告警消息源？

    理论上，实时计算可以支持将结果写出到任意系统，但在告警、通知等场景下，实时计算仍然推荐将结果数据投递到MQ之类的消息存储，这样能更好地保证告警准确性。


## 代码说明

经过传感器上传的数据进入Log，当行数据格式如下。

```
{
    "sid": "t_xxsfdsad", 
    "s_value": "85.5", 
    "s_ts": "1515228763"
} 
```

定义Log源表为s\_sensor\_data，结构如下。

```
CREATE TABLE s_sensor_data (
    s_id    VARCHAR,
    s_value VARCHAR,
    s_ts    VARCHAR,
    ts        AS CAST(FROM_UNIXTIME(CAST(s_ts AS BIGINT)) AS TIMESTAMP),
      WATERMARK FOR ts AS withOffset(ts, 10000)
) WITH (
    TYPE='sls',
    endPoint ='http://cn-hangzhou-corp.sls.aliyuncs.com',
      accessId ='xxxxxxxxxxx',
      accessKey ='xxxxxxxxxxxxxxxxxxxxxxxxxxxx',
      project ='ali-cloud-streamtest',
      logStore ='stream-test',
);       
```

定义传感器和设备关联RDS维表为d\_sensor\_device\_data，结构如下。

```
CREATE TABLE d_sensor_device_data (
    s_id    VARCHAR,
    s_type    VARCHAR,
    device_id BIGINT,
    factory_id BIGINT,
    PERIOD FOR SYSTEM_TIME，
    PRIMARY KEY(s_id)
) WITH (
    TYPE='RDS',
     url='',
     tableName='test4',
     userName='test',
     password='XXXXXX'
);    
```

定义触发告警逻辑MQ表为r\_monitor\_data，结构如下。

```
CREATE TABLE r_monitor_data (
    ts    VARCHAR,
    device_id    BIGINT,
    factory_id    BIGINT,
    device_TEMP    DOUBLE,
    device_PRES DOUBLE
) WITH (
    TYPE='MQ'
);    
```

定义存储结果数据的HybridDB表定义为r\_device\_data，结构如下。

```
CREATE TABLE r_device_data (
    ts    VARCHAR,
    device_id BIGINT,
    factory_id BIGINT,
    device_temp    DOUBLE,
    device_pres DOUBLE,
    PRIMARY KEY(ts, device_id)
) WITH (
    TYPE='HybridDB'
); 
```

先考虑将传感器数据按分钟级别进行汇总，打平为一个宽表。为了方便后续代码维护，请使用结构化代码和View。

```
--先获取每个传感器对应的设备、厂区。
CREATE VIEW v_sensor_device_data
AS
SELECT
    s.ts,
    s.s_id,
    s.s_value,
    d.s_type,
    d.device_id,
    d.factory_id
FROM
    s_sensor_data s
JOIN
    d_sensor_device_data FOR SYSTEM_TIME AS OF PROCTIME() as d
ON
    s.s_id = d.s_id；

--打平为一张宽表。
CREATE VIEW v_device_data
AS
SELECT
    --使用滚窗的起始时间作为该条记录的时间。
    CAST(TUMBLE_START(v.ts, INTERVAL '1' MINUTE) AS VARCHAR) as ts,
    v.device_id,
    v.factory_id,
    CAST(SUM(IF(v.s_type = 'TEMP', v.s_value, 0)) AS DOUBLE)/CAST(SUM(IF(v.s_type = 'TEMP', 1, 0)) AS DOUBLE) device_temp, --这里用于计算这一分钟的温度平均值
    CAST(SUM(IF(v.s_type = 'PRES', v.s_value, 0)) AS DOUBLE)/CAST(SUM(IF(v.s_type = 'PRES', 1, 0)) AS DOUBLE) device_pres --这里用于计算这一分钟的压力平均值
FROM
    v_sensor_device_data v
GROUP BY
    TUMBLE(v.ts, INTERVAL '1' MINUTE), v.device_id, v.factory_id;        
```

上述是计算的核心逻辑，统计一分钟内温度和压力的平均值，作为这一分钟的温度值、压力值。由于使用的是Tumbling Window，也就意味着在每分钟结束时产出一份数据。接下来将数据过滤写出到MQ和HybridDB。

## Demo示例以及源代码

根据上文介绍的IoT解决方案，为您创建了一个包含完整链路的Demo示例。

```
--过滤温度大于80摄氏度的传感器，并写出到MQ触发告警。
INSERT INTO r_monitor_data
SELECT
    ts,
    device_id,
    factory_id,
    device_temp,
    device_pres
FROM
    v_device_data
WHERE
    device_temp > 80.0;

--将数据写出到HybridDB，用于后续的分析。
INSERT INTO r_device_data
SELECT
    ts,
    device_id,
    factory_id,
    device_temp,
    device_pres
FROM
    v_device_data;    
```

您可以参考[Demo代码](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/157666/cn_zh/1584415277248/demo.sql)，注册上下游数据，制定自己的IoT解决方案。使用示例时，上下游存储请注意以下几点：

-   选择Logtail采集ECS上的文本信息作为源表。
-   RDS作为维表。
-   MQ和HybridDB作为结果表。

