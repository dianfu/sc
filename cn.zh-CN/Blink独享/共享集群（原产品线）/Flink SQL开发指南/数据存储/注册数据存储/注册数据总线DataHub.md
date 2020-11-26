# 注册数据总线DataHub

本文为您介绍数据总线DataHub所需的参数信息。

## 什么是数据总线

阿里云流数据处理平台DataHub是流式数据（Streaming Data）的处理平台，提供对流式数据的发布（Publish）、订阅（Subscribe）和分发功能，让您可以轻松构建基于流式数据的分析和应用。实时计算Flink版使用DataHub作为流式数据存储源头或输出目的端。

## 注册存储

**说明：** 使用注册存储功能前，请先完成实时计算Flink版对存储设备的访问授权，授权方法请您参见[共享模式角色授权](/cn.zh-CN/Blink独享/共享集群（原产品线）/准备工作/角色授权/共享模式角色授权.md)和[独享模式角色授权](/cn.zh-CN/Blink独享/共享集群（原产品线）/准备工作/角色授权/独享模式角色授权.md)。

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  在页面顶部，单击**开发**。
3.  在左侧导航栏，单击**数据存储**。
4.  在页面左上角，单击**注册与网络**。
5.  在**注册数据存储与网络探测**对话框，配置存储设备参数。
6.  单击**注册**。

## 参数说明

-   EndPoint

    填写DataHub的EndPoint。不同地域DataHub有不同的EndPoint，具体请参见[DataHub域名列表](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC)。

    **说明：** EndPoint地址格式为`http://****.aliyun-inc.com`，不能以`/`结尾。

    -   独享模式：使用DataHub VPC EndPoint。例如，**华东1（杭州）**使用`http://dh-cn-hangzhou-int-vpc.aliyuncs.com```。

        **说明：** 独享模式集群所在的区域与DataHub所在区域要求一致。

    -   共享模式（已停购）：使用经典网络ECS EndPoint。例如，**华东1（杭州）**使用`http://dh-cn-hangzhou.aliyun-inc.com`。

        **说明：** 共享模式项目所在的区域与DataHub所在区域不要求一致，但不一致可能增加网络延时。

-   Project

    填写DataHub的Project。

    **说明：** 实时计算Flink版暂不支持跨属主的数据存储注册。例如，A用户拥有DataHub的ProjectA，但B用户希望在实时计算Flink版使用ProjectA。如果您需要使用跨属主的数据存储，可使用明文方式，详情请参见[明文方式](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/数据存储概述.md)。


## 常见问题

Q：注册数据存储失败的原因有哪些？

A：实时计算Flink版的数据存储页面能够协助您完成数据管理，注册数据存储功能是使用相关存储SDK访问各类存储的。如果注册数据存储失败，请从以下方面进行排查：

-   是否已经开通并拥有DataHub的Project。请登录[DataHub控制台](https://datahub.console.aliyun.com/datahub?spm=a2c4g.11186623.2.16.2d882a36F2C50S)，查看您是否具备访问对应Project的权限。
-   您是否为DataHub Project的属主。跨属主的数据存储不能注册。
-   您填写的DataHub的EndPoint和Project是否完全正确。DataHub的EndPoint必须以`http`开头，且不能以`/`结尾。
-   您填写的DataHub EndPoint必须为经典网络地址，而非VPC地址。实时计算Flink版暂不支持VPC内部地址。
-   请不要重复注册，实时计算Flink版提供注册检测机制，重复注册会导致注册失败。

Q：为什么数据抽样仅支持时间抽样？

A：DataHub定位是流数据存储，对外提供的接口仅有时间参数。因此，实时计算Flink版仅提供基于时间的抽样。

