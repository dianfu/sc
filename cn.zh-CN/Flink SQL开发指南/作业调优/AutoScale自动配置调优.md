# AutoScale自动配置调优 {#concept_nc3_3qd_2hb .concept}

为了解决您使用AutoConf自动配置调优功能时频繁启停作业的问题，实时计算3.0及以上版本提供了AutoScale自动配置调优功能。作业启动后，系统会根据资源配置规则，自动进行作业的调优，直到满足设定的调优目标，全程无需人工介入。

**说明：** 

-   AutoScale自动配置调优功能仅支持实时计算3.0及以上版本。
-   升级实时计算3.0版本前，需要删除所有低于3.0版本的PlanJson，并[重新获取配置资源](https://help.aliyun.com/knowledge_detail/109143.html)。
-   升级实时计算3.0版本前，需要删除所有低于3.0版本的PlanJson，并重新获取配置资源。

## 启动AutoScale自动配置调优 {#section_i8i_2ew_dx0 .section}

作业上线的过程中即可完成AutoScale自动配置调优的开启。

1.  登录作业编辑页面。
    1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
    2.  单击页面顶部的**开发**。
    3.  在**作业开发**区域，双击目标文件夹或目标作业名，进入作业编辑页面。
2.  单击页面顶部菜单栏中的**上线**，进入上线新版本页面。
3.  在**初始资源**页面，选择初始资源类型，单击**下一步**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/156776770946751_zh-CN.png)

    -   **上次自动调优**：使用上次AutoScale的planjson配置启动作业。满足以下所有条件后即可选择**上次自动调优**功能。
        -   作业已开启AutoScale并完成迭代。
        -   作业为**暂停**状态。
        -   在**资源配置**页面**获取AutoScale配置信息**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/156776770959274_zh-CN.jpg)

    -   **系统分配**：使用上一次AutoScale自动生成的方法。新增作业或者作业逻辑没有修改且兼容实时计算版本时，可以选择**系统分配**。
    -   **手动资源配置**：使用手动生成的资源配置方法。手动重新生成或者修改AutoScale时，需要选择**手动资源配置**。
4.  在**数据检查**，通过上线检查后，单击**下一步**。
5.  在**资源配置**，配置**自动调优**信息后，单击**下一步**。

    |参数|说明|
    |--|--|
    |**自动调优**|选择**开启**，开启自动调优功能。|
    |**planJson最大CU数**|作业可用的资源上限，单位为CU，1CU=1 Core + 4G RAM。最大CU数需小于项目可用CU数。|
    |**调优策略**|目前**调优策略**仅支持**数据滞留时间**。系统会根据选择的**调优策略**和**期望值**对作业自动调优。|
    |**期望值**|数据滞留时间阈值。当数据源的数据滞留时间超过该值时，触发AutoScale，优化作业并发数。|

    **说明：** 例如，调优策略设置数据滞留时间的期望值为5（秒）。如果此时作业的数据滞留时间大于5秒，则系统会不断进行自动调优，直至数据滞留时间小于5秒。

6.  在**上线作业**，单击**上线**。
7.  启动作业。启动作业步骤参见[启动](cn.zh-CN/Flink SQL开发指南/作业开发/启动.md#)。

## 关闭AutoScale {#section_6s6_c9x_6s1 .section}

**说明：** 启动作业时开启AutoScale自动调优功能后，才可以进行关闭AutoScale自动调优操作。

您可以在作业运行期间，动态关闭AutoScale自动调优功能。

1.  登录作业运维页面。
    1.  登录[实时计算控制台](https://stream.console.aliyun.com)。
    2.  单击页面顶部的**运维**。
    3.  在**作业列表**区域，单击**作业名称**下的目标作业名。
2.  单击页面右上角的**关闭自动调优**。
3.  单击**确认**。

**说明：** 作业在上线时启动了AutoScale后，**运维**界面**自动调优**列下的操作按键才能生效。

## AutoScale效果查看 {#section_lc7_zff_e5j .section}

**说明：** 请在运维界面查看。

-   AutoScale Metric

    在**数据曲线** \> **OverView**页面，可以查看AutoScale的信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/156776770946778_zh-CN.png)

    |曲线名称|说明|
    |----|--|
    |**AutoScale的成功和失败数**|AutoScale成功和未成功执行的次数。|
    |**AutoScale的成功和失败数**|执行AutoScale时消耗的CPU量。|
    |**AutoScale的成功和失败数**|执行AutoScale时消耗的内存量。|

-   AutoScale PlanJson信息

    在**属性参数** \> **资源配置** \> **AutoScale PlanJson调优历史**，单击目标版本，查看AutoScale生成的PlanJson的信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/156776770946779_zh-CN.png)


## AutoScale可能出现的问题 {#section_s8d_ajs_z4v .section}

-   作业自动重启

    AutoScale根据实际流量来动态调整并发数以及资源，因此可能会在流量上涨以及流量下降时，通过自动重启触发资源调整。

-   短时间出现数据传输延迟

    流量低谷时AutoScale会降低并发数、减少资源分配。当流量上涨后，流量低谷时的资源配置，会导致吞吐能力不够和数据传输的延迟。

-   作业无法恢复

    在部分场景下AutoScale无法有效工作，导致作业延迟和作业频繁的进行配置调整。

-   并发数先减少后增多

    包含窗口函数或聚合函数的作业，在状态（State）数量的增加时，访问State的性能衰减，作业启动时的并发数减少。随着数据积累，并发数会逐渐增大，直至State数量达到稳定。


## FAQ {#section_psn_fcf_2hb .section}

-   Q：作业**启动**时产生报错：`ERR_ID：CLI-00000001`。

    A：参见[启动报错：CLI-00000001](https://help.aliyun.com/knowledge_detail/119399.html)。

-   Q：作业**上线**时参数校验报错：`resource validate failed！please modify your planJson or max CU and try again.`。

    A： 以下2种场景可能导致该报错：

    -   PlanJson资源文件和实际作业不匹配
        -   报错原因：PlanJson资源文件和实际作业不匹配，导致能够关联（Chain）在一起的节点没有关联在一起，使作业所需资源变大。
        -   解决方案：重新生成配置信息，操作步骤参见[启动报错：CLI-00000001](https://help.aliyun.com/knowledge_detail/119399.html)。
    -   配置的可用资源过小
        -   报错原因：作业所需的CPU或者内存中的一个资源超过最大资源。
        -   解决方案：返回**上一步**，增加最大可用资源的CU数。
-   Q：AutoScale没有启动。

    A： 排查方法如下：

    -   检查作业是否频繁运行失败（Failover）。AutoScale开启的前提是作业本身逻辑正确并且能够稳定运行。
    -   查看**JobManager**的相关日志，检查是否存在系统异常。
-   Q：AutoScale没有效果。

    A：请按照以下顺序进行排查：

    1.  检查最大资源限制，确认作业已使用资源是否达到最大资源限制。
    2.  检查作业数据源（Source）节点的逻辑，查看Source节点是否关联（Chain）过多的Operator。若关联过多Operator，请手动编辑PlanJson，在一些关键的位置进行关联（Chain）拆分。解决方案参见[手动配置调优](cn.zh-CN/Flink SQL开发指南/作业调优/手动配置调优.md#)。
    3.  查看**JobManager**的相关日志，检查是否存在系统异常。

