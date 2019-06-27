# OpenAPI概览 {#concept_y4c_3cg_qgb .concept}

OpenAPI概览为您列举实时计算所支持的OpenAPI。

## 作业 {#section_jxm_4dg_qgb .section}

|OpenAPI|描述|
|-------|--|
|[CheckRawPlanJson](cn.zh-CN/OpenAPI/作业/CheckRawPlanJson.md#)|检测作业PlanJson的获取状态|
|[CommitJob](cn.zh-CN/OpenAPI/作业/CommitJob.md#)|提交作业|
|[CreateJob](cn.zh-CN/OpenAPI/作业/CreateJob.md#)|创建作业|
|[DeleteJob](cn.zh-CN/OpenAPI/作业/DeleteJob.md#)|删除作业|
|[GetJob](cn.zh-CN/OpenAPI/作业/GetJob.md#)|获取job信息|
|[ListJob](cn.zh-CN/OpenAPI/作业/ListJob.md#)|搜索Job|
|[OfflineJob](cn.zh-CN/OpenAPI/作业/OfflineJob.md#)|下线作业|
|[StartJob](cn.zh-CN/OpenAPI/作业/StartJob.md#)|启动作业|
|[UpdateJob](cn.zh-CN/OpenAPI/作业/UpdateJob.md#)|更新作业|
|[ValidateJob](cn.zh-CN/OpenAPI/作业/ValidateJob.md#)|作业语法检查|

## 文件夹 {#section_xzx_pdg_qgb .section}

|OpenAPI|描述|
|-------|--|
|[MVFolder](cn.zh-CN/OpenAPI/文件夹/MVFolder.md#)|移动文件夹|
|[CreateFolder](cn.zh-CN/OpenAPI/文件夹/CreateFolder.md#)|创建文件夹|
|[DeleteFolder](cn.zh-CN/OpenAPI/文件夹/DeleteFolder.md#)|删除文件夹|
|[GetFolder](cn.zh-CN/OpenAPI/文件夹/GetFolder.md#)|获取文件夹|
|[ListChildFolder](cn.zh-CN/OpenAPI/文件夹/ListChildFolder.md#)|获取子目录|

## 实例 {#section_c3b_rdg_qgb .section}

|OpenAPI|描述|
|-------|--|
|[BatchGetInstanceRunSummary](cn.zh-CN/OpenAPI/实例/BatchGetInstanceRunSummary.md#)|批量获取实例运行信息|
|[GetInstanceResource](cn.zh-CN/OpenAPI/实例/GetInstanceResource.md#)|获取运行实例使用资源|
|[GetInstance](cn.zh-CN/OpenAPI/实例/GetInstance.md#)|获取运行实例详情|
|[GetInstanceCheckpoint](cn.zh-CN/OpenAPI/实例/GetInstanceCheckpoint.md#)|获取运行实例的Checkpoint|
|[GetInstanceConfig](cn.zh-CN/OpenAPI/实例/GetInstanceConfig.md#)|获取作业运行参数|
|[GetInstanceDetail](cn.zh-CN/OpenAPI/实例/GetInstanceDetail.md#)|获取实例运行的DAG图|
|[GetInstanceExceptions](cn.zh-CN/OpenAPI/实例/GetInstanceExceptions.md#)|获取运行实例的Failover信息|
|[GetInstanceFinalState](cn.zh-CN/OpenAPI/实例/GetInstanceFinalState.md#)|获取运行实例最终状态|
|[GetInstanceMetric](cn.zh-CN/OpenAPI/实例/GetInstanceMetric.md#)|获取运行实例的Metric信息|
|[GetInstanceRunSummary](cn.zh-CN/OpenAPI/实例/GetInstanceRunSummary.md#)|获取作业运行实例的运行概要|
|[ListInstance](cn.zh-CN/OpenAPI/实例/ListInstance.md#)|获取某个项目下所有的运行实例|
|[ModifyInstanceState](cn.zh-CN/OpenAPI/实例/GetInstanceResource.md#)|修改实例状态|
|[GetRawPlanJson](cn.zh-CN/OpenAPI/实例/GetRawPlanJson.md#)|获取原始执行计划|

## Package {#section_izp_sdg_qgb .section}

|OpenAPI|描述|
|-------|--|
|[CreatePackage](cn.zh-CN/OpenAPI/Package/CreatePackage.md#)|获取Package信息|
|[DeletePackage](cn.zh-CN/OpenAPI/Package/DeletePackage.md#)|删除Package|
|[GetPackage](cn.zh-CN/OpenAPI/Package/GetPackage.md#)|获得Package信息|
|[ListPackage](cn.zh-CN/OpenAPI/Package/ListPackage.md#)|搜索Package|
|[GetRefPackageJob](cn.zh-CN/OpenAPI/Package/GetRefPackageJob.md#)|获取引用特定Package的作业|
|[UpdatePackage](cn.zh-CN/OpenAPI/Package/UpdatePackage.md#)|更新Package|

## Queue {#section_myq_tdg_qgb .section}

|OpenAPI|描述|
|-------|--|
|[CreateQueue](cn.zh-CN/OpenAPI/Queue/CreateQueue.md#)|创建Queue|
|[DeleteQueue](cn.zh-CN/OpenAPI/Queue/DeleteQueue.md#)|删除Queue|
|[BindQueue](cn.zh-CN/OpenAPI/Queue/BindQueue.md#)|绑定项目运行需要的Queue|
|[UnbindQueue](cn.zh-CN/OpenAPI/Queue/UnbindQueue.md#)|解绑项目运行的Queue|
|[GetClusterQueueInfo](cn.zh-CN/OpenAPI/Queue/GetClusterQueueInfo.md#)|获取集群中Queue的信息|
|[ListProjectBindQueue](cn.zh-CN/OpenAPI/Queue/ListProjectBindQueue.md#)|查询项目绑定的队列信息|
|[ListProjectBindQueueResource](cn.zh-CN/OpenAPI/Queue/ListProjectBindQueueResource.md#)|查询项目绑定的队列的资源信息|

## Cluster {#section_klr_5gj_b81 .section}

|OpenAPI|描述|
|-------|--|
|[CreateCluster](cn.zh-CN/OpenAPI/Cluster/CreateCluster.md#)|创建集群|
|[ExpandCluster](cn.zh-CN/OpenAPI/Cluster/ExpandCluster.md#)|后付费集群升级|
|[DestroyCluster](cn.zh-CN/OpenAPI/Cluster/DestroyCluster.md#)|注销集群|
|[ShrinkCluster](cn.zh-CN/OpenAPI/Cluster/ShrinkCluster.md#)|集群缩容|
|[ListCluster](cn.zh-CN/OpenAPI/Cluster/ListCluster.md#)|查询已存在集群信息|
|[ModifyMasterSpec](cn.zh-CN/OpenAPI/Cluster/ModifyMasterSpec.md#)|变更Master机型规格|
|[GetClusterResource](cn.zh-CN/OpenAPI/Cluster/GetClusterResource.md#)|获取集群资源状态信息|
|[GetClusterEngineVersions](cn.zh-CN/OpenAPI/Cluster/GetClusterEngineVersions.md#)|获取集群引擎版本|
|[GetClusterDetails](cn.zh-CN/OpenAPI/Cluster/GetClusterDetails.md#)|获取集群详情|

## Project {#section_odd_84b_xmx .section}

|OpenAPI|描述|
|-------|--|
|[CreateProject](cn.zh-CN/OpenAPI/Project/CreateProject.md#)|创建项目|
|[DeleteProject](cn.zh-CN/OpenAPI/Project/DeleteProject.md#)|删除项目|
|[GetProject](cn.zh-CN/OpenAPI/Project/GetProject.md#)|获取项目详情|
|[ListProject](cn.zh-CN/OpenAPI/Project/ListProject.md#)|查询您已有的项目|

