# 独享集群Blink版本管理 {#concept_1925983 .concept}

实时计算为独享模式集群提供**版本管理**功能，您可自行管理集群对应的Blink引擎版本。

## 登录版本管理页面 {#section_9py_z1e_wgh .section}

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  将鼠标悬停至控制台页面右上角个人账户位置，单击**项目管理**。
3.  在左侧导航栏，单击**集群管理** \> **集群列表**。
4.  在**集群列表**区域，单击目标集群**操作**列下的**更多** \> **版本管理**。

## 设置为current {#section_cbh_eel_4uq .section}

1.  [登录版本管理页面](#section_9py_z1e_wgh)。
2.  在**已安装版本**页面，单击目标版本**操作**列下的**设置为current**。

## 卸载 {#section_9vl_yg6_7kk .section}

**说明：** 

-   不能卸载已设置为current的版本。
-   不能卸载作业已引用的版本。

1.  [登录版本管理页面](#section_9py_z1e_wgh)。
2.  在**已安装版本**页面，单击目标版本**操作**列下的**卸载**。

## 安装 {#section_e0z_1j0_rqk .section}

**说明：** 

-   最多安装3个版本（已拥有超过3个版本的集群不受此限制）。若需安装新版本，需预先删除原有版本，使已安装版本数小于3。
-   一次只能安装一个版本，安装过程中集群状态显示为**版本部署中**。
-   不可安装已安装过的版本。
-   如果安装过程出现异常，将以短信方式发送监控告警。
-   每个版本设有服役时间（默认一年），版本退役后您可以继续使用此版本，但阿里云团队不再负责维护。

可安装版本分为以下三类。

|标签|说明|
|--|--|
|**stable**|当前推荐安装的稳定版本。|
|**beta**|测试版本。 **说明：** 在特定场景下才可安装使用，非特定场景不保证版本质量和稳定性，不建议安装。

 |
|无标签|历史稳定版本。|

安装步骤如下：

1.  [登录版本管理页面](#section_9py_z1e_wgh)。
2.  在**可安装版本**页面，单击目标版本**操作**列下的**安装**。

## 版本切换 {#section_ucz_ee8_tuy .section}

1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
2.  单击页面的顶部**开发**。
3.  在**作业开发**页面双击文件夹或目标作业，进入作业编辑页面。
4.  在作业编辑页面的右下角，单击版本信息。
5.  在选项列表中选择目标版本。

## 常见问题 {#section_yg7_3mu_nga .section}

-   Q：安装引擎出现报错信息`blink-<version> already installed`。

    A：该版本已经安装，无需再次安装。

-   Q：安装引擎出现报错信息`Flink versions exceeded max limitation:3`。

    A：超出版本限制。若需安装新版本，需预先删除原有版本，使已安装版本数小于3。

-   Q：卸载引擎出现报错信息`Node:<nodeName> in project:<projectName> still ref the version:<blinkVersion>`。

    A：线上存在引用此版本引擎的作业，请根据报错信息中的作业名称和项目名称，将该作业先进行下线。

-   Q：单击**语法检查**或**上线**产生如下报错。

    ``` {#codeblock_sh4_g7z_y96 .lanuage-sql}
    code:[30006], brief info:[blink script not exist, please check blink version], context info:[blink script:[/home/admin/blink/blink-2.2.6-hotfix0/bin/flink], blink version:[/home/admin/blink/blink-2.2.6-hotfix0/bin/flink]]
    ```

    A：该作业使用的引擎不存在。进入**版本管理** \> **已安装版本**查看是否存在该版本引擎。

    -   不存在：请切换或安装此版本引擎。
    -   存在：请提交工单。

