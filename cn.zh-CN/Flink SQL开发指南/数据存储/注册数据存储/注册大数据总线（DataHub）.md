# 注册大数据总线（DataHub） {#concept_62475_zh .concept}

本文为您介绍注册大数据总线（DataHub）数据存储所需的参数信息，以及注册存储过程中的常见问题。

大数据总线DataHub，为阿里云数加平台提供了大数据的入口服务。结合阿里云众多云产品，DataHub可以构建一站式的数据处理平台。实时计算通常使用DataHub作为流式数据存储的源头和目的端。

## 登录注册页面 {#section_qyb_c0d_h7w .section}

**说明：** 使用注册存储功能前，请先完成实时计算对存储设备的访问授权，授权方法请您参见[共享模式角色授权](../../../../cn.zh-CN/准备工作/共享模式角色授权.md#)和[独享模式角色授权](../../../../cn.zh-CN/准备工作/独享模式角色授权.md#)。

登录步骤请参见[存储注册方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_sx2_4tz_zfb)。

## 参数说明 {#section_ifr_4k5_xfb .section}

-   EndPoint

    填写DataHub的EndPoint。不同地域下DataHub有不同的Endpoint，具体请参见

     [DataHub访问控制](https://help.aliyun.com/document_detail/47442.html?spm=a2c4g.11186623.6.545.41a01a12lmpjWC)。

    **说明：** EndPoint地址格式为`http://****.aliyun-inc.com`，不能以`/`结尾。

    -   共享模式：使用经典网络ECS Endpoint。例如，**华东1（杭州）**使用`http://dh-cn-hangzhou.aliyun-inc.com`。

        **说明：** 共享模式项目所在的区域与DataHub所在区域不要求一致，但不一致可能增加网络延时。

    -   独享模式：使用VPC ECS Endpoint。例如，**华东1（杭州）**使用`http://dh-cn-hangzhou.aliyun-inc.com`。

        **说明：** 独享模式集群所在的区域与DataHub所在区域要求一致。

-   Project

    填写DataHub的Project。

    **说明：** 跨属主的数据存储不能注册。例如A用户拥有DataHub的ProjectA，但B用户希望在实时计算使用ProjectA，目前实时计算暂不支持这类使用场景下注册，若需使用可使用明文方式，具体参考数据存储概述-明文方式[明文方式](cn.zh-CN/Flink SQL开发指南/数据存储/数据存储概述.md#section_qx2_4tz_zfb)。


## 常见问题 {#section_c93_ix6_xnp .section}

Q: 注册数据存储失败的原因有哪些？

A: 实时计算的数据存储页面能够协助您完成数据管理，注册数据存储功能是使用相关存储SDK代为访问各类存储。如果注册数据存储失败，请从以下方面进行排查：

-   是否已经开通并拥有DataHub的Project。请登录[DataHub控制台](https://datahub.console.aliyun.com/datahub?spm=a2c4g.11186623.2.16.2d882a36F2C50S)，查看您是否具备访问对应Project的权限。
-   您是否为DataHub Project的属主。跨属主的数据存储不能注册。
-   您填写的DataHub的Endpoint和Project是否完全正确。DataHub的Endpoint必须以`http`开头，且不能以`/`结尾。
-   您填写的DataHub Endpoint必须为经典网络地址，而非VPC地址。目前实时计算暂不支持VPC内部地址。
-   请不要重复注册，实时计算提供注册检测机制，重复注册会导致注册失败。

Q: 为什么数据抽样仅支持时间抽样？

A: DataHub定位是流数据存储，对外提供的接口也仅有时间参数。因此，实时计算也仅能提供基于时间的抽样。

