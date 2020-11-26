---
keyword: 注册表格存储Tablestore
---

# 注册表格存储Tablestore

本文为您介绍注册表格存储Tablestore所需的参数信息。

## 什么是表格存储Tablestore

表格存储Tablestore是构建在阿里云飞天分布式系统之上的NoSQL数据存储服务。表格存储能够提供海量结构化数据的存储服务和实时访问服务。Tablestore具备低延迟和运算复杂度低的特点，因此适合作为实时计算Flink版的数据存储维表或结果表。

## 注册存储

**说明：** 使用注册存储功能前，请先完成实时计算Flink版对存储设备的访问授权，授权方法请您参见[独享模式角色授权](/intl.zh-CN/独享模式/准备工作/角色授权/独享模式角色授权.md)

1.  登录[实时计算控制台](https://stream-ap-southeast-3.console.aliyun.com)。
2.  在页面顶部，单击**开发**。
3.  在左侧导航栏，单击**数据存储**。
4.  在页面左上角，单击**注册与网络**。
5.  在**注册数据存储与网络探测**对话框，配置存储设备参数。
6.  单击**注册**。

## 参数说明

-   Endpoint
    -   填写Tablestore的Endpoint。请在[表格存储控制台](https://ots.console.aliyun.com)查看Tablestore的Endpoint信息，填写Tablestore的VPC地址。具体步骤请参见[VPC地址](/intl.zh-CN/功能介绍/基础概念/服务地址.md)。
    -   Tablestore访问网络类型设置为**允许任意网络访问**。操作步骤如下：
        1.  登录[表格存储控制台](https://ots.console.aliyun.com/)。
        2.  单击目标**实例名称**。
        3.  在**网络管理**页签，单击**更改**。
        4.  选择**允许任意网络访问**。
        5.  单击**确定**。
-   实例名称

    填写实例名称。


