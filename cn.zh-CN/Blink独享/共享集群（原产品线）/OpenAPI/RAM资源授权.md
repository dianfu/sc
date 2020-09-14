# RAM资源授权

刚创建的子账号无法使用主账号的资源，您需要通过RAM授权的方式，赋予子账号操作主账号资源的权限。

默认情况下，您对自己创建的资源拥有完整的操作权限，可以使用本文档中列举的OpenAPI对资源进行操作。

**说明：** 如果您不需要跨账户授权和访问实例资源，您可以跳过该章节。

## 授权策略（Policy）

授权策略（Policy）描述授权的具体内容。授权内容主要包含效果（Effect）、资源（Resource）、对资源所授予的操作权限（Action）以及限制条件（Condition）这几个基本元素。以下内容以OpenAPI网关为例，为您介绍授权策略（Policy）。

-   系统授权策略

    OpenAPI网关已经预置了AliyunApiGatewayFullAccess和AliyunApiGatewayReadOnlyAccess两个系统权限，您可以在**RAM控制台** \> **策略管理**查看。

    -   AliyunApiGatewayFullAccess：管理员权限，拥有主帐号下OpenAPI分组、OpenAPI、流控策略、应用等所有资源的管理权限。
    -   AliyunApiGatewayReadOnlyAccess：可以查看主帐号下OpenAPI分组、OpenAPI、流控策略、应用等所有资源，但不可以操作。
-   授权策略

    您可以在**RAM控制台** \> **策略管理** \> **自定义授权策略**查看已经定义好的自定义授权。

    您也可以根据需要自定义管理权限，支持更为精细化的授权。可以为某个操作或者某个资源授权。例如，GetUsers的编辑权限。自定义策略的查看、创建、修改和删除方法清参见[授权策略管理](/cn.zh-CN/权限策略管理/查看权限策略基本信息.md)。授权策略内容填写方法请参见[权限策略基本元素](/cn.zh-CN/权限策略管理/权限策略语言/权限策略基本元素.md)、[权限策略语法和结构](/cn.zh-CN/权限策略管理/权限策略语言/权限策略语法和结构.md)。

-   示例

    允许所有的查看操作。

    ```
    {
      "Version": "1",
      "Statement": [
        {
      "Action": "apigateway:Describe*",
          "Resource": "*",
          "Effect": "Allow"
        }
      ]
    }
    ```

    -   Action（操作名称列表）格式

        "Action":"<service-name\>:<action-name\>"

        -   service-name：阿里云产品名称，请填写`apigateway`。
        -   action-name：OpenAPI接口名称，支持通配符 `*`。

            ```
            "Action": "apigateway:Describe*" --表示所有的查询操作。 
            "Action": "apigateway:*" --表示API网关所有操作。 
            ```

    -   Resource（操作对象列表）
        -   Resource通常指操作对象

            OpenAPI网关中的OpenAPI分组、流控策略、应用都被称为Resource，语法格式：acs:<service-name\>:<region\>:<account-id\>:<relative-id\>。其中：

            -   acs：Alibaba Cloud Service的首字母缩写，表示阿里云的公共云平台。
            -   service-name：阿里云产品名称，请填写`apigateway`。
            -   region：地区信息，可以使用通配符号`*`来表示所有区域。
            -   account-id：账号ID，例如，`1234567890123456`，也可以使用`*`代替。
            -   relative-id：与服务相关的资源描述部分，其语义由具体服务指定，该部分的格式支持树状结构（类似文件路径）。以OSS为例，表示一个OSS对象的格式为：`relative-id = “mybucket/dir1/object1.jpg”`。
        -   Resource示例

            acs:apigateway:$regionid:$accountid:apigroup/$groupId

        -   Resource语法

            acs:apigateway:\*:$accountid:apigroup/


## 鉴权规则

以OpenAPI网关为例。

|action-name|资源（Resource）|
|-----------|------------|
|AbolishApi|acs:apigateway:$regionid:$accountid:apigroup/$groupId|

