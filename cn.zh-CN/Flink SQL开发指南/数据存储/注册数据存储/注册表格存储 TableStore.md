# 注册表格存储 TableStore {#concept_62477_zh .concept}

本文为您介绍注册表格存储 TableStore数据存储所需的参数信息。

表格存储 Table Store是构建在阿里云飞天分布式系统之上的NoSQL数据存储服务。表格存储能够提供海量结构化数据的存储服务和实时访问服务。实时计算要求访问低延迟，同时对关系型代数需求较低，因此，实时计算适合使用TableStore作为数据存储维表和结果表。

**说明：** 使用注册存储功能前，请先完成实时计算对存储设备的访问授权，授权方法请参见[共享模式角色授权](../../../../cn.zh-CN/准备工作/共享模式角色授权.md#)。

## 登录注册页面 {#section_7ps_pov_b4z .section}

登录步骤请参见[存储注册方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_sx2_4tz_zfb)。

## 参数说明 {#section_wff_snb_yfb .section}

-   Endpoint
    -   填写TableStore的Endpoint。请在[OTS控制台](https://ots.console.aliyun.com)查看TableStore的Endpoint信息，填写TableStore私网地址。具体步骤请参见[私网地址](../../../../cn.zh-CN/开发指南/基础概念/服务地址.md#section_c7o_v0k_41k)。
    -   TableStore访问网络类型设置为**允许任意网络访问**。登录[OTS控制台](https://ots.console.aliyun.com/)，选择**实例详情** \> **实例网络类型** \> **更改** \> **允许任意网络访问**。
-   实例名称

    填写TableStore的实例名称。


