# 注册日志服务（Log Service） {#concept_62476_zh .concept}

本文为您介绍注册日志服务（Log Service）数据存储所需的参数信息，以及存储注册过程中的常见问题。

日志服务（Log Service）是针对日志场景的一站式解决方案。日志服务提供海量日志数据采集、订阅、转储与查询功能。在您使用日志服务完成了对ECS日志的管理的前提下，实时计算无需进行数据迁移，即可对接日志服务的LogHub存储。

**说明：** 使用注册存储功能前，请先完成实时计算对存储设备的访问授权，授权方法请参见[共享模式角色授权](../../../../cn.zh-CN/准备工作/共享模式角色授权.md#)。

## 登录注册页面 {#section_v8y_1g8_x3z .section}

登录步骤请参见[存储注册方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_sx2_4tz_zfb)。

## 参数说明 {#section_rxr_1hb_yfb .section}

-   Endpoint

    填写日志服务的Endpoint。不同的地域下日志服务有不同的Endpoint，请参见日志服务服务入口[服务入口](../../../../cn.zh-CN/API 参考/服务入口.md#)。

    **说明：** 

    -   Endpoint需增加`http://`开头，且不能使用`/`结尾，如`http://cn-hangzhou-intranet.log.aliyuncs.com`。
    -   实时计算和日志服务均处于阿里云内网，建议您填写经典网络或VPC网络服务入口。不建议填写公网服务入口，填写公网服务入口可能导致性能问题和外网宽带的消耗。
-   Project

    填写日志服务的Project。

    **说明：** 跨属主的数据存储不能注册。例如，A用户拥有日志服务的Project A，但B用户希望在实时计算使用Project A。目前，实时计算暂不支持这类场景下的数据存储注册方式，请使用[明文方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_qx2_4tz_zfb)。


## 常见问题 {#section_4rl_xnv_nph .section}

Q: 注册数据存储失败的原因有哪些？

A: 实时计算的数据存储页面能够协助您完成数据管理，注册数据存储功能是使用相关存储SDK代为访问各类存储。如果注册数据存储失败，请从以下方面进行排查：

-   是否已经开通并拥有LogService的Project。请登录[日志服务控制台](https://sls.console.aliyun.com/)，查看您是否具备访问对应Project的权限。
-   您是否为LogService Project的属主。跨属主的数据存储不能注册。
-   您填写的LogService的Endpoint和Project是否完全正确。LogService的Endpoint必须以`http`开头，且不能以`/`结尾。
-   您填写的LogService的Endpoint必须为经典网络地址，而非VPC地址。目前实时计算暂不支持VPC内部地址。
-   请不要重复注册，实时计算提供注册检测机制，重复注册会导致注册失败。

Q: 为什么数据抽样仅支持时间抽样？

A: LogService定位是流数据存储，对外提供的接口也仅有时间参数。因此，实时计算也仅能提供基于时间的抽样。

