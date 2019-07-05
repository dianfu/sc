# VPC访问授权 {#concept_66270_zh .concept}

实时计算共享模式访问阿里云专有网络（VPC）中的存储资源前，需要进行VPC访问授权。本文为您介绍VPC访问授权的流程。

## 背景 {#section_a3c_swz_zfb .section}

实时计算共享模式处于阿里云经典网络，若需要访问阿里云VPC中的存储资源（例如，VPC中的RDS、MySQL、HybridDB数据库等），则需要完成VPC访问授权。

**说明：** 

-   独享模式集群处于阿里云VPC，无需进行VPC访问授权。
-   完成VPC访问授权后，实时计算访问对应的存储资源可能存在带宽受限等性能问题。通常不建议在实时计算共享模式下访问VPC中的存储资源。

## VPC访问授权步骤 {#section_c3c_swz_zfb .section}

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  将鼠标悬停至页面右上角账号名称。
3.  在下拉菜单中，单击**项目管理**。
4.  在左侧导航栏中单击**VPC访问授权**。
5.  在VPC访问授权页面的右上角，单击**新增授权**。
6.  在**授权流计算访问VPC**页面，输入相应的配置参数。

    |参数|说明|
    |--|--|
    |名称|VPC的名称|
    |地域|VPC中存储设备所在的区域。|
    |VPC ID|VPC中存储设备的VPC网络ID。 **说明：** RDS VPC网络ID查看步骤如下：

    1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)
    2.  在页面左上角，选择实例所在地域。
    3.  单击目标实例ID。
    4.  单击左侧导航栏中的**数据库连接**。
    5.  在**实例连接** \> **数据库连接** \> **网络类型**中，查看RDS的VPC ID。例如，`vpc-bp1lysht98wrvl9n3****`。
 |
    |Instance ID|VPC中存储设设备的实例ID。 **说明：** RDS中实例ID查看步骤如下：

    1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)
    2.  在页面左上角，选择实例所在地域。
    3.  单击目标实例ID，进入基本信息页面。
    4.  在**基本信息** \> **实例ID**中，查看RDS实例的ID。
 |
    |Instance Port|VPC中存储设备的端口ID。 **说明：** RDS端口查询方法请参见[查看实例的内外网地址及端口信息](../../../../cn.zh-CN/RDS for MySQL 用户指南/数据库连接/查看实例的内外网地址及端口信息.md#)。

 |


## 常见问题 {#section_i8x_2ec_5ca .section}

Q：明文方式中，专有网络存储设备URL参数如何添加？

A：使用明文方式引用VPC中的存储时，DDL WITH参数中的URL参数值需填写VPC访问授权页面中的**Mapping IP**和**Mapping IP**参数，例如，`url='jdbc:mysql://<mappingIP>:<mappingPort>/<databaseName>'`。**Mapping IP**和**Mapping IP**信息查看步骤如下：

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  将鼠标悬停至页面右上角账号名称。
3.  在下拉菜单中，单击**项目管理**。
4.  在左侧导航栏中单击**VPC访问授权**。
5.  在VPC访问授权页面查看**Mapping IP**和**Mapping IP**信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40858/156232101336284_zh-CN.png)


