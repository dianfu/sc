# 创建InfluxDB结果表

本文为您介绍如何创建实时计算Flink版InfluxDB结果表，以及创建结果表时使用的WITH参数和类型映射。

**说明：**

-   InfluxDB暂不支持注册存储功能。
-   本文仅适用于Blink 3.5.0-hotfix及以上版本。

## DDL定义

实时计算Flink版支持使用InfluxDB作为结果输出，示例代码如下。

```
create table stream_test_influxdb(
    `metric` varchar,
    `timestamp` BIGINT,
    `tag_value1` varchar,
    `field_fieldValue1` Double
)with(
    type = 'influxdb',
    endpoint = 'http://service.cn.influxdb.aliyuncs.com:****',
    database = '<yourDatabaseName>',
    batchPutsize = '1',
    username = '<yourDatabaseUserName>',
    password = '<yourDatabasePassword>'
);
```

建表默认格式：

-   第0列：metric（VARCHAR），必填。
-   第1列：timestamp（BIGINT），必填，单位为毫秒。
-   第2列：tag\_value1（VARCHAR），必填，最少填写一个。
-   第3列：field\_fieldValue1（DOUBLE），必填，最少填写一个。

    写入多个field\_fieldValue时，您需要按照如下格式填写。

    ```
    field_fieldValue1 类型,
    field_fieldValue2 类型,
    ...      
    field_fieldValueN 类型
    ```

    示例如下。

    ```
    field_fieldValue1 Double,
    field_fieldValue2 INTEGER,
    ...      
    field_fieldValueN INTEGER
    ```


**说明：** 结果表中只支持metric、timestamp、tag\_\*和field\_\*，不能出现其他的字段。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为InfluxDB。|
|endpoint|InfluxDB的Endpoint|是|在InfluxDB中，Endpoint是VPC网络地址，例如：https://localhost:3242或http://localhost:8086。

Endpoint支持HTTP和HTTPS。 |
|database|InfluxDB的数据库名|是|例如：db-blink或者blink。|
|batchPutSize|批量提交的记录条数|否|默认每次提交500个数据点。|
|username|InfluxDB的用户名|是|需要对写入的数据库有写权限。|
|password|InfluxDB的密码|是|默认值为0。|
|retentionPolicy|保留策略|否|为空时，默认填写为每个database的默认保留策略。|

## 类型映射

|InfluxDB字段类型|实时计算Flink版字段类型|
|------------|--------------|
|BOOLEAN|BOOLEAN|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|

