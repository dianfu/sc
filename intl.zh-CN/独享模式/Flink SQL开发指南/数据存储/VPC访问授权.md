# VPC访问授权

实时计算共享模式访问阿里云专有网络（VPC）中的存储资源前，需要进行VPC访问授权。本文为您介绍VPC访问授权的流程。

## 背景

实时计算共享模式属于阿里云经典网络，如果需要访问阿里云VPC中的存储资源（目前仅支持VPC授权RDS），则需要完成VPC访问授权。

**说明：**

-   独享模式集群处于阿里云VPC，无需进行VPC访问授权。
-   完成VPC访问授权后，实时计算访问对应的存储资源可能存在带宽受限等性能问题。不建议在实时计算共享模式下访问VPC中的存储资源。
-   实时计算共享模式已于2019年12月24日正式下线，不再支持共享模式新项目的购买，仅支持原有项目的扩缩容、续费操作。如果有新购需求，推荐使用实时计算独享模式或Flink云原生模式。

## VPC访问授权步骤

1.  登录[实时计算控制台](https://stream-ap-southeast-3.console.aliyun.com)。
2.  将鼠标悬停至页面右上角账号名称。
3.  在下拉菜单中，单击**项目管理**。
4.  在左侧导航栏中，单击**VPC访问授权**。
5.  在VPC访问授权页面的右上角，单击**新增授权**。
6.  在**授权流计算访问VPC**页面，输入相应的配置参数。

    |参数|说明|
    |--|--|
    |**名称**|VPC的名称。|
    |**地域**|VPC中存储设备所在的区域。|
    |**VPC ID**|VPC中存储设备的VPC网络ID。RDS VPC网络ID查看步骤如下：     1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
    2.  在页面左上角，选择实例所在地域。
    3.  单击目标实例ID。
    4.  单击左侧导航栏中的**数据库连接**。
    5.  在**实例连接** \> **数据库连接** \> **网络类型**中，查看RDS的VPC ID。例如，`vpc-bp1lysht98wrvl9n3****`。 |
    |**Instance ID**|VPC中存储设备的实例ID。 RDS中实例ID查看步骤如下：     1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
    2.  在页面左上角，选择实例所在地域。
    3.  单击目标实例ID，进入基本信息页面。
    4.  在**基本信息** \> **实例ID**中，查看RDS实例的ID。 |
    |**Instance Port**|VPC中存储设备的端口ID。RDS端口查询方法请参见[查看或修改内外网地址和端口](/intl.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。|


## 常见问题

Q：明文方式中，如何添加专有网络存储设备的URL参数？

A：在使用明文方式引用VPC中的存储时，DDL WITH参数中的URL参数值需填写VPC访问授权页面中的**Mapping IP**和**Mapping Port**参数，例如，url='jdbc:mysql://<mappingIP\>:<mappingPort\>/<databaseName\>'。**Mapping IP**和**Mapping Port**信息查看步骤如下：

1.  登录[实时计算控制台](https://stream-ap-southeast-3.console.aliyun.com)。
2.  将鼠标悬停至页面右上角账号名称。
3.  在下拉菜单中，单击**项目管理**。
4.  在左侧导航栏中，单击**VPC访问授权**。
5.  在VPC访问授权页面，查看**Mapping IP**和**Mapping Port**信息。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7221659951/p36284.png)


