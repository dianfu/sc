# 注册云数据库RDS版 {#concept_62478_zh .concept}

本文为您介绍注册云数据库RDS版数据存储所需的参数信息。，以及连接过程中的常见问题。

云数据库RDS版是一种稳定可靠、可弹性伸缩的在线数据库服务。目前实时计算支持包括MySQL在内的RDS数据库引擎。

**说明：** 

-   使用注册存储功能前，请先完成实时计算对存储设备的访问授权，授权方法请参见[共享模式角色授权](../../../../cn.zh-CN/准备工作/共享模式角色授权.md#)和[独享模式角色授权](../../../../cn.zh-CN/准备工作/独享模式角色授权.md#)。
-   高频或高并发写入场景，一般不建议使用RDS作为实时计算作业的结果表，存在死锁风险。建议使用表格存储作为结果表（详情请参见[创建表格存储（Table Store）结果表](cn.zh-CN/Flink SQL开发指南/Flink SQL/DDL语句/创建数据结果表/创建表格存储 TableStore结果表.md#)）。
-   云数据库RDS8.0版本不支持注册存储功能，请使用[明文方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_qx2_4tz_zfb)。

## 登录注册页面 {#section_ndz_i7l_tbz .section}

登录步骤请参见[存储注册方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_sx2_4tz_zfb)。

## 参数说明 {#section_vnh_vgd_yfb .section}

**说明：** 存储注册过程中系统会为RDS自动配置IP白名单。

|参数名称|说明|
|----|--|
|Region|选择RDS的地域，请您选择RDS所在地域。|
|Instance|填写RDS实例ID。 **说明：** 请填写实例ID，不是实例名称。

 |
|DBName|填写RDS中DataBase的名称。 **说明：** DataBase是RDS的数据库名称，不是Instance的名称。

 |
|User Name|RDS数据库登录名称。|
|Password|RDS数据库登录密码。|
|网络类型|默认为内网。|
|白名单授权|默认设置。实时计算存储注册方式为RDS自动添加IP白名单。|

信息填写：

## 常见问题 {#section_ejy_2zg_yfb .section}

Q：存储注册过程中出现`操作错误：未在VPC中授权`？

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40857/156352507033245_zh-CN.png)

A：注册的RDS实例为专有网络（VPC）实例。对于VPC实例，请先完成实时计算对VPC的访问授权，授权步骤请参见[VPC访问授权](cn.zh-CN/Flink SQL开发指南/数据存储/VPC访问授权.md#)。

