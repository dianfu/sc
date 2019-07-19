# 注册分析型数据库MySQL版 {#concept_lwm_5ty_zfb .concept}

本文为您介绍注册分析型数据库MySQL版数据存储所需的参数信息。

## 登录注册页面 {#section_k5u_02q_ul7 .section}

登录步骤请参见[存储注册方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_sx2_4tz_zfb)。

## 参数说明 {#section_kxb_15y_zfb .section}

|参数名称|说明|
|----|--|
|URL|AnalyticDB for MySQL控制台链接信息串。 -   共享模式：请使用AnalyticDB for MySQL的经典网络的连接点
-   独享模式：请使用AnalyticDB for MySQL的VPC网络的连接点。

 |
|Database|AnalyticDB for MySQL数据库名称，即AnalyticDB for MySQL实例名称。|
|AccessKey ID|阿里云账号安全信息页面的AccessID。|
|AccessKey Secret|阿里云账号安全信息页面的AccessKey。|

**说明：** 

-   URL​地址查询方法
    1.  登录[AnalyticDB 控制台](https://ads.console.aliyun.com/?spm=a2c4g.11186623.2.23.2c952b809T8asM)。
    2.  单击对应的实例名称，进入基本信息页面。
    3.  在**连接信息** \> **连接地址**中查看相应的连接地址。
-   AccessId、AccessKey信息查询方法参见[如何查看AccessID、AccessKey信息](https://help.aliyun.com/knowledge_detail/94557.html)。

