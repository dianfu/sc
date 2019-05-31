# AutoScale自动配置调试 {#concept_nc3_3qd_2hb .concept}

为了解决您使用AutoConf自动配置调优功能时频繁启停作业的问题，实时计算3.0及以上版本提供了AutoScale自动配置调优功能。作业启动后，系统会根据资源配置规则，自动进行作业的调优，直到满足设定的调优目标，全程无需人工介入。

**说明：** 

-   AutoScale自动配置调优功能仅支持实时计算3.0及以上版本。
-   升级实时计算3.0版本前，需要删除所有低于3.0版本的PlanJson，并[重新获取配置资源](https://help.aliyun.com/knowledge_detail/109143.html)。

## 初始化资源配置 {#section_tth_1xu_dnt .section}

作业上线时，需要选择作业初始的资源方法。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746751_zh-CN.png)

-   系统分配：使用上一次AutoScale自动生成的方法。新增作业，或者作业逻辑没有修改且实时计算版本兼容时，可以选择系统分配。
-   手动资源配置：使用手动生成的资源配置方法。手动重新生成或者修改过AutoScale时，需要选择手动资源配置。

## 开启AutoScale {#section_u5f_ky2_2hb .section}

作业上线时，在**资源配置**步骤，选择**开启**自动调优，即可设置自动调优参数。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746762_zh-CN.png)

-   最大CU数

    作业可用的资源上限，单位为CU，1CU=1 Core + 4G RAM。设置了最大资源数，作业运行期间使用的资源不会超过该上限 。最大CU数需小于项目可用CU数。

-   调优策略

    系统会根据选择的策略和期望值对作业自动调优以达到期望值（目前策略只支持数据滞留时间）。

-   期望值

    数据滞留时间阈值，当Source的数据滞留时间超过该阈值时，触发AutoScale，优化作业并发度。

    **说明：** 例如：调优策略设置数据滞留时间的期望值：5（秒）。假如，此时作业的数据滞留时间大于5秒，系统会不断进行自动调优，直至数据滞留时间降至小于5秒。


## 暂停AutoScale {#section_6s6_c9x_6s1 .section}

启动作业时，如果开启了AutoScale，可以在作业运行期间，动态暂停AutoScale。在作业**运维**页面，单击暂停按钮即可。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746765_zh-CN.png)

**说明：** 作业在上线时启动了AutoScale后，**运维**界面**自动调优**列下的操作按键才能生效。

## AutoScale效果查看 {#section_lc7_zff_e5j .section}

-   AutoScale Metric

    作业的数据曲线页面可以看到AutoScale的历史信息，包括：AutoScale成功和失败的次数，运行期间作业实际申请的CPU和内存变化曲线等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746778_zh-CN.png)

    |曲线名称|说明|
    |----|--|
    |Auto Scaling Successes and Failures|AutoScale成功和未成功执行的次数。|
    |CPUs Consumed By Auto Scaling|执行AutoScale时消耗的CPU量。|
    |Memory Consumed By Auto Scaling|执行AutoScale时消耗的内存量。|

-   AutoScale生成的PlanJson

    作业的**属性参数**页面可以看到AutoScale的生成的PlanJson的信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746779_zh-CN.png)


## FAQ {#section_psn_fcf_2hb .section}

Q：作业启动时，资源文件校验报错节点不存在或者节点不匹配，该如何处理？

-   节点不存在

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968746782_zh-CN.png)

-   节点不匹配

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968846783_zh-CN.png)


A：该问题产生的原因是因为作业的plan发生了改变（SQL修改了或blink版本升级不兼容），此时，需要重新手动生成一次资源配置，上线时初始资源选择手动资源配置。

Q：上线时，参数校验报错：`resource validate failed`，该如何处理？

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155926968846784_zh-CN.png)

A：

-   PlanJson资源文件和实际作业不匹配，导致能够chain在一起的节点没有chain在一起，导致作业所需资源异常变大。此时，需要重新手动生成一次资源配置，上线时初始资源选择**手动资源配置**。
-   配置的可用资源过小，当作业所需的CPU或者内存中的一个资源超过最大资源时会报错，此时，需要返回上一步，增加最大可用资源的CU数。

Q：AutoScale没有启动?

A：

-   检查作业是否频繁Failover。AutoScale开启的前提是作业本身逻辑没有问题，能够稳定运行。
-   查看**JobManager**的相关日志，查看是否存在系统处理的异常问题。

Q：AutoScale没有效果?

A：

-   检查最大资源限制，查看作业已经使用的资源是否已经达到了最大资源限制。
-   检查作业source节点的逻辑，查看source节点是否chain了太多Operator。如果是，需要手动编辑PlanJson，在一些关键的位置手动打断chain。解决方案请参见[手动配置调优](cn.zh-CN/Flink SQL开发指南/配置调优/手动配置调优.md#)。
-   查看**JobManager**的相关日志，查看是否存在系统处理的异常问题。

