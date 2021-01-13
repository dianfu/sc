# OpenAPI DEMO

本文为您介绍公共云环境下，使用实时计算OpenAPI方式操作实时计算作业所需的POM依赖和OpenAPI DEMO。

## POM依赖

```
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-foas</artifactId>
    <version>2.8.0</version>
</dependency>
```

**说明：** 如果需要依赖aliyun-java-sdk-core，请使用4.3.0及以上版本。4.3.0版本依赖示例如下。

```
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-core</artifactId>
    <version>4.3.0</version>
</dependency>
```

## OpenAPI DEMO

```
package sample;

import com.aliyuncs.AcsRequest;
import com.aliyuncs.AcsResponse;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.foas.model.v20181111.*;
import com.aliyuncs.http.FormatType;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;
import com.google.gson.Gson;

import java.util.HashMap;
import java.util.Map;

/**
 * 适用于公共云环境。
 *
 * 作业的一般流程是：
 * 如果有依赖package，先创建package。
 * 创建Job->获取planjson->更新planjson到job->上线job->启动job->停止instance。
 * Instance的状态一般如下（流作业为例，括号里的为动作):
 * Job上线->UNKNOWN[启动job]->WAITING[等待]->RUNNING[暂停]->PAUSED[恢复]->RUNNING[停止]->TERMINATED
 *
 * Note:以下示例仅供演示foas接口的调用方式，不保证逻辑上的严谨性，请按照自己的场景重新组织调用逻辑。
 */
public class PublicSample {

    //填写购买的foas所在的regionId，根据您的实际情况修改。
    private static final String regionId = "cn-shanghai";

    //云账号AK，支持子账号，需要自行保证该账号具有访问实时计算服务的权限（子账号需要得到主账号访问实时计算服务的权限授权）。
    private static final String accessId = "<yourAccessId>";
    private static final String accessKey = "<yourAccessSecret>";

    private static final String projectName = "<yourProjectName>";
    private static final String jobName = "<yourJobName>";

    // 文件夹与Linux文件系统格式相同，以/开始，表示根目录，只支持绝对路径，不支持相对路径。
    private static final String folderName = "/新手任务";
    private static final String clusterId = "<yourClusterId>";//可以通过listProjectBindQueue接口来获得。
    private static final String queueName = "root.<queueName>";//可以通过listProjectBindQueue接口来获得。
    private static final String engineVersion = "current";//Blink或者Flink的版本，current表示使用推荐版本，beta表示使用测试版本。
    private static final String packageName = "<packageName>";

    private static final String sql =
            "--SQL\n" +
                    "--********************************************************************--\n" +
                    "--CreateTime: 2018-12-29 14:35:04\n" +
                    "--Comment: 请输入业务注释信息\n" +
                    "--********************************************************************--\n" +
                    "\n" +
                    "CREATE TABLE datahub_source (`name` INT, num VARCHAR) WITH (\n" +
                    "    type = 'random'\n" +
                    ");\n" +
                    "CREATE TABLE datahub_sink (`name` INT, cnt BIGINT) WITH (\n" +
                    "    type = 'PRINT'\n" +
                    ");\n" +
                    "INSERT INTO\n" +
                    "    datahub_sink\n" +
                    "SELECT\n" +
                    "    `name`,\n" +
                    "    COUNT (1)\n" +
                    "FROM\n" +
                    "    datahub_source\n" +
                    "GROUP BY\n" +
                    "    `name`";

    private static final String properties =
            "#checkpoint模式：EXACTLY_ONCE或者AT_LEAST_ONCE\n" +
                    "blink.checkpoint.mode=EXACTLY_ONCE\n" +
                    "#checkpoint间隔时间，单位毫秒\n" +
                    "blink.checkpoint.interval.ms=180000\n" +
                    "#rocksdb的数据生命周期，单位毫秒\n" +
                    "#state.backend.rocksdb.ttl.ms=129600000\n";

    private static <T extends AcsResponse> T getResponse(IAcsClient client, AcsRequest<T> request) {
        AcsResponse response = null;
        try {
            //必须设置为json。
            request.setHttpContentType(FormatType.JSON);
            request.setAcceptFormat(FormatType.JSON);

            response = client.getAcsResponse(request);
        } catch (Exception e) {
            //处理异常的逻辑，请注意异常中的requestId字段，排查问题时，需要提供该值给服务端。
            if (e instanceof ServerException) {
                ServerException serverException = (ServerException) e;
                System.out.println(serverException.getRequestId());//本次请求的requestId。
                System.out.println(serverException.getErrCode());//本次请求失败的错误码，用于排查错误。
            }
            System.out.println(e.getMessage());
        }
        return (T) response;
    }

    private static String getClusterMetricJson(String clusterId) {
        return String.format(
                        "{\n" +
                        "\t\"queries\": [ {\n" +
                        "\t\t\"metric\": \"%s.system.cpu.user\",\n" +
                        "\t\t\"aggregator\": \"avg\",\n" +
                        "\t\t\"granularity\": \"20s\",\n" +
                        "\t\t\"downsample\": \"avg\"\n" +
                        "\t}, {\n" +
                        "\t\t\"metric\": \"%s.system.cpu.iowait\",\n" +
                        "\t\t\"aggregator\": \"avg\",\n" +
                        "\t\t\"granularity\": \"20s\",\n" +
                        "\t\t\"downsample\": \"avg\"\n" +
                        "\t}],\n" +
                        "\t\"start\": %s,\n" +
                        "\t\"end\": %s,\n" +
                        "\t\"limit\": \"avg:sample:50\"\n" +
                        "}",
                clusterId, clusterId, System.currentTimeMillis() - 3600 * 1000, System.currentTimeMillis()
        );
    }

    public static void main(String[] args) throws Exception {

        IClientProfile profile = DefaultProfile.getProfile(regionId, accessId, accessKey);
        DefaultAcsClient client = new DefaultAcsClient(profile);
        client.setAutoRetry(false);//不进行自动重试。

        //创建一个独享集群，该接口是异步接口，调用返回后需要调用listCluster接口来获取集群的状态来判断是否创建完成。
        CreateClusterRequest createClusterRequest = new CreateClusterRequest();
        createClusterRequest.setDescription("test cluster");
        createClusterRequest.setDisplayName("aaaa");
        createClusterRequest.setOrderId("yyyyy");//填写购买独享集群下单后生成的订单ID。
        createClusterRequest.setUserOssBucket("myBucket");
        createClusterRequest.setUserVpcId("xxxxx");
        createClusterRequest.setUserVSwitch("yyyy");
        createClusterRequest.setZoneId("cn-shanghai-f");
        CreateClusterResponse createClusterResponse = PublicSample.getResponse(client, createClusterRequest);
        System.out.println(new Gson().toJson(createClusterResponse));//该response中含有clusterId信息，这个是集群的索引，后面的大部分接口都需要使用到。

        //根据条件搜索集群，不设置的条件留null。
        ListClusterRequest listClusterRequest = new ListClusterRequest();
        listClusterRequest.setClusterId(clusterId);
        listClusterRequest.setRegion(regionId);
        ListClusterResponse listClusterResponse = PublicSample.getResponse(client, listClusterRequest);
        System.out.println(new Gson().toJson(listClusterResponse));

        //在该集群上创建queue，只能创建一级queue。
        CreateQueueRequest createQueueRequest = new CreateQueueRequest();
        createQueueRequest.setClusterId(clusterId);
        createQueueRequest.setQueueName(queueName);
        createQueueRequest.setMaxVcore(300);
        createQueueRequest.setMaxMemMB(4 * 1024);
        CreateQueueResponse createQueueResponse = PublicSample.getResponse(client, createQueueRequest);
        System.out.println(new Gson().toJson(createQueueResponse));

        //获取集群的引擎版本信息。
        GetClusterEngineVersionsRequest getClusterEngineVersionsRequest = new GetClusterEngineVersionsRequest();
        getClusterEngineVersionsRequest.setClusterId(clusterId);
        GetClusterEngineVersionsResponse getClusterEngineVersionsResponse = PublicSample.getResponse(client, getClusterEngineVersionsRequest);
        System.out.println(new Gson().toJson(getClusterEngineVersionsResponse));

        //获取该集群的资源信息。
        GetClusterResourceRequest getClusterResourceRequest = new GetClusterResourceRequest();
        getClusterResourceRequest.setClusterId(clusterId);
        GetClusterResourceResponse getClusterResourceResponse = PublicSample.getResponse(client, getClusterResourceRequest);
        System.out.println(new Gson().toJson(getClusterResourceResponse));

        //获取集群的queue信息，包括所有的queue和各个queue的资源信息。
        GetClusterQueueInfoRequest getClusterQueueInfoRequest = new GetClusterQueueInfoRequest();
        getClusterQueueInfoRequest.setClusterId(clusterId);
        GetClusterQueueInfoResponse getClusterQueueInfoResponse = PublicSample.getResponse(client, getClusterQueueInfoRequest);
        System.out.println(new Gson().toJson(getClusterQueueInfoResponse));

        //获取集群的detail信息。
        GetClusterDetailsRequest getClusterDetailsRequest = new GetClusterDetailsRequest();
        getClusterDetailsRequest.setClusterId(clusterId);
        GetClusterDetailsResponse getClusterDetailsResponse = PublicSample.getResponse(client, getClusterDetailsRequest);
        System.out.println(new Gson().toJson(getClusterDetailsResponse));

        //获取集群的metrics曲线，最多只能获取最近两小时的数据。
        GetClusterMetricsRequest getClusterMetricsRequest = new GetClusterMetricsRequest();
        getClusterMetricsRequest.setClusterId(clusterId);
        getClusterMetricsRequest.setMetricJson(getClusterMetricJson(clusterId));//指标的名称请查看文档。
        GetClusterMetricsResponse getClusterMetricsResponse = PublicSample.getResponse(client, getClusterMetricsRequest);
        System.out.println(new Gson().toJson(getClusterMetricsResponse));

        //注意：以下操作独享集群的接口只支持按量付费的独享集群。
        //扩容集群，该接口是异步接口，需要通过集群状态来判断是否操作完成。
        ExpandClusterRequest expandClusterRequest = new ExpandClusterRequest();
        expandClusterRequest.setClusterId(clusterId);
        expandClusterRequest.setModel("Ecs_4c8g");//该参数可选，当前不支持混布，只能填与原有的机型一致的机型，或者不填写。
        expandClusterRequest.setCount(20);//目标集群台数。
        ExpandClusterResponse expandClusterResponse = PublicSample.getResponse(client, expandClusterRequest);
        System.out.println(new Gson().toJson(expandClusterResponse));

        //修改集群master的机型配置，只能更改master机型，不能修改master数量。
        //注意：该接口是异步接口，需要通过集群状态来判断是否操作完成。
        ModifyMasterSpecRequest modifyMasterSpecRequest = new ModifyMasterSpecRequest();
        modifyMasterSpecRequest.setClusterId(clusterId);
        modifyMasterSpecRequest.setMasterTargetModel("Ecs_8c16g");
        ModifyMasterSpecResponse modifyMasterSpecResponse = PublicSample.getResponse(client, modifyMasterSpecRequest);
        System.out.println(new Gson().toJson(modifyMasterSpecResponse));

        //缩容集群，缩容有两种参数提供形式（当前仅支持第一种形式）:
        //1.直接填需要去除的实例instanceId（可以通过getClusterDetails接口获得）。
        //2.提供每种机型的目标台数。
        //注意：该接口是异步接口，需要通过集群状态来判断是否操作完成。
        ShrinkClusterRequest shrinkClusterRequest = new ShrinkClusterRequest();
        shrinkClusterRequest.setClusterId(clusterId);
        shrinkClusterRequest.setInstanceIds("111,222,333");
        //shrinkClusterRequest.setModelTargetCount("{"Ecs_4c8g":2,"Ecs_8c16g":7}");
        ShrinkClusterResponse shrinkClusterResponse = PublicSample.getResponse(client, shrinkClusterRequest);
        System.out.println(new Gson().toJson(shrinkClusterResponse));

        //销毁集群，只能销毁按量付费的集群。注意: 该接口操作风险高，请务必谨慎操作。
        DestroyClusterRequest destroyClusterRequest = new DestroyClusterRequest();
        destroyClusterRequest.setClusterId(clusterId);
        DestroyClusterResponse destroyClusterResponse = PublicSample.getResponse(client, destroyClusterRequest);
        System.out.println(new Gson().toJson(destroyClusterResponse));

        //创建project，该接口分两种场景：
        //1.购买的共享集群，需要提供订单ID。
        //2.独享集群上创建project，需要提供独享集群的clusterId。
        CreateProjectRequest createProjectRequest = new CreateProjectRequest();
        //createProjectRequest.setOrderId();//在共享集群上创建project时必填，填写购买的订单id。
        createProjectRequest.setDescription("test project");
        createProjectRequest.setDeployType("cell");//在共享集群上创建project时填public。
        createProjectRequest.setClusterId(clusterId);//在共享集群上创建project时可不填。
        createProjectRequest.setName(projectName);
        CreateProjectResponse createProjectResponse = PublicSample.getResponse(client, createProjectRequest);
        System.out.println(new Gson().toJson(createProjectResponse));

        // 绑定queue到project，该操作类似于将资源（某个queue）指定给某个project使用，只有将资源绑定到了某个project上，
        // 该project里的作业才能使用该资源。当前只支持一对一的关系，即一个project只能绑定一个queue，一个queue只能被绑定至一个project上。
        BindQueueRequest bindQueueRequest = new BindQueueRequest();
        bindQueueRequest.setProjectName(projectName);
        bindQueueRequest.setClusterId(clusterId);
        bindQueueRequest.setQueueName(queueName);
        BindQueueResponse bindQueueResponse = PublicSample.getResponse(client, bindQueueRequest);
        System.out.println(new Gson().toJson(bindQueueResponse));

        //获取指定project已绑定的资源。
        ListProjectBindQueueRequest listProjectBindQueueRequest = new ListProjectBindQueueRequest();
        listProjectBindQueueRequest.setProjectName(projectName);
        ListProjectBindQueueResponse listProjectBindQueueResponse = PublicSample.getResponse(client, listProjectBindQueueRequest);
        System.out.println(new Gson().toJson(listProjectBindQueueResponse));

        //获取project详情。
        GetProjectRequest getProjectRequest = new GetProjectRequest();
        getProjectRequest.setProjectName(projectName);
        GetProjectResponse getProjectResponse = PublicSample.getResponse(client, getProjectRequest);
        System.out.println(new Gson().toJson(getProjectResponse));

        //根据条件搜索project。
        ListProjectRequest listProjectRequest = new ListProjectRequest();
        listProjectRequest.setName(projectName);
        listProjectRequest.setDeployType("cell");
        listProjectRequest.setRegion("cn-shanghai");
        ListProjectResponse listProjectResponse = PublicSample.getResponse(client, listProjectRequest);
        System.out.println(new Gson().toJson(listProjectResponse));

        //从project解绑资源。注意：解绑前，请先将使用了该资源的作业全部下线。
        UnbindQueueRequest unbindQueueRequest = new UnbindQueueRequest();
        unbindQueueRequest.setProjectName(projectName);
        unbindQueueRequest.setClusterId(clusterId);
        unbindQueueRequest.setQueueName(queueName);
        UnbindQueueResponse unbindQueueResponse = PublicSample.getResponse(client, unbindQueueRequest);
        System.out.println(new Gson().toJson(unbindQueueResponse));

        //删除project。注意:删除project是高危操作，请务必谨慎操作。删除的条件是该project下的所有作业均已下线。
        DeleteProjectRequest deleteProjectRequest = new DeleteProjectRequest();
        deleteProjectRequest.setProjectName(projectName);
        DeleteProjectResponse deleteProjectResponse = PublicSample.getResponse(client, deleteProjectRequest);
        System.out.println(new Gson().toJson(deleteProjectResponse));

        //获取文件夹列表。
        ListChildFolderRequest listChildFolderRequest = new ListChildFolderRequest();
        listChildFolderRequest.setPath("/");
        listChildFolderRequest.setProjectName(projectName);
        ListChildFolderResponse listChildFolderResponse = PublicSample.getResponse(client, listChildFolderRequest);
        System.out.println(new Gson().toJson(listChildFolderResponse));

        //获取文件夹。
        GetFolderRequest getFolderRequest = new GetFolderRequest();
        getFolderRequest.setPath(folderName);
        getFolderRequest.setProjectName(projectName);
        GetFolderResponse getFolderResponse = PublicSample.getResponse(client, getFolderRequest);
        System.out.println(new Gson().toJson(getFolderResponse));

        Long folderId;
        GetFolderResponse.Folder folder = getFolderResponse.getFolder();

        if (folder == null || folder.getFolderId() == null) {
            //创建文件夹。
            CreateFolderRequest createFolderRequest = new CreateFolderRequest();
            createFolderRequest.setPath(folderName);
            createFolderRequest.setProjectName(projectName);
            CreateFolderResponse createFolderResponse = PublicSample.getResponse(client, createFolderRequest);
            System.out.println(new Gson().toJson(createFolderResponse));
            folderId = createFolderResponse.getFolderId();
        } else {
            folderId = folder.getFolderId();
        }

        // sdk的Package存在您的OSS上，需要将Package通过OSS console或者OSS sdk上传到OSS上。
        // 此处需要将OSS meta信息提供给foas，
        // 并对foas授权Bucket读取权限(ram role:AliyunStreamDefaultRole)，foas在使用时会从用户指定的OSS和Bucket上下载Package。
        // 请确保OSS的region与foas所在的region一致。
        // 如果project建在独享集群上，则不需要提供Endpoint，Bucket等信息，只需要提供package的OSSPath。
        // 用户的OSS信息在创建独享集群的时候已经提供过了，不需要重复提供。
        CreatePackageRequest createPackageRequest = new CreatePackageRequest();
        createPackageRequest.setProjectName(projectName);
        createPackageRequest.setPackageName(packageName);
        createPackageRequest.setType("jar");
        createPackageRequest.setDescription("test package");
        createPackageRequest.setOssEndpoint("oss-cn-hangzhou.aliyuncs.com");//提供的endpoint需要保证网络可通达。
        createPackageRequest.setOssBucket("your bucket");
        createPackageRequest.setOssOwner("111111111");//OSS的主账号uid。
        createPackageRequest.setOssPath("aaa/bbb/ccc.jar");
        createPackageRequest.setMd5("123456");//可不填。不填，则跳过md5校验；填写，则在下载时进行完整性校验。
        CreatePackageResponse createPackageResponse = PublicSample.getResponse(client, createPackageRequest);
        System.out.println(new Gson().toJson(createPackageResponse));

        //更新package。
        UpdatePackageRequest updatePackageRequest = new UpdatePackageRequest();
        updatePackageRequest.setProjectName(projectName);
        updatePackageRequest.setPackageName(packageName);
        updatePackageRequest.setOssPath("aaa/bbb/ddd.jar");
        UpdatePackageResponse updatePackageResponse = PublicSample.getResponse(client, updatePackageRequest);
        System.out.println(new Gson().toJson(updatePackageResponse));

        //获取package详情。
        GetPackageRequest getPackageRequest = new GetPackageRequest();
        getPackageRequest.setProjectName(projectName);
        getPackageRequest.setPackageName(packageName);
        GetPackageResponse getPackageResponse = PublicSample.getResponse(client, getPackageRequest);
        System.out.println(new Gson().toJson(getPackageResponse));

        //根据条件搜索package。
        ListPackageRequest listPackageRequest = new ListPackageRequest();
        listPackageRequest.setProjectName(projectName);
        listPackageRequest.setPackageName("aa");//模糊匹配。
        listPackageRequest.setType("jar");
        listPackageRequest.setPageIndex(1);//可选，默认1。
        listPackageRequest.setPageSize(30);//可选，默认10。
        ListPackageResponse listPackageResponse = PublicSample.getResponse(client, listPackageRequest);
        System.out.println(new Gson().toJson(listPackageResponse));

        //列举project绑定的cluster和queue。
        ListProjectBindQueueRequest listProjectBindQueueRequest2 = new ListProjectBindQueueRequest();
        listProjectBindQueueRequest2.setProjectName(projectName);
        ListProjectBindQueueResponse listProjectBindQueueResponse2 = PublicSample.getResponse(client, listProjectBindQueueRequest2);
        System.out.println(new Gson().toJson(listProjectBindQueueResponse2));

        //查询project下绑定的queue的资源。
        ListProjectBindQueueResourceRequest listProjectBindQueueResourceRequest = new ListProjectBindQueueResourceRequest();
        listProjectBindQueueResourceRequest.setProjectName(projectName);
        ListProjectBindQueueResourceResponse listProjectBindQueueResourceResponse = PublicSample.getResponse(client, listProjectBindQueueResourceRequest);
        System.out.println(new Gson().toJson(listProjectBindQueueResourceResponse));

        //根据条件搜索project下的作业。
        ListJobRequest listJobRequest = new ListJobRequest();
        listJobRequest.setProjectName(projectName);
        listJobRequest.setJobType("flink_stream");
        listJobRequest.setApiType("sql");
        //该参数控制返回的job信息中是否返回较长的字段（例如code）。填false，则返回的只有部分字段；不填或者填true，则返回所有字段。
        listJobRequest.setIsShowFullField(false);
        ListJobResponse listJobResponse = PublicSample.getResponse(client, listJobRequest);
        System.out.println(new Gson().toJson(listJobResponse));

        //获取作业。
        GetJobRequest getJobRequest = new GetJobRequest();
        getJobRequest.setProjectName(projectName);
        getJobRequest.setJobName(jobName);
        GetJobResponse getJobResponse = PublicSample.getResponse(client, getJobRequest);
        System.out.println(new Gson().toJson(getJobResponse));

        GetJobResponse.Job job = getJobResponse.getJob();
        if (job == null || job.getJobName() == null) {
            //创建作业。
            CreateJobRequest createJobRequest = new CreateJobRequest();
            createJobRequest.setProjectName(projectName);
            createJobRequest.setJobName(jobName);
            createJobRequest.setCode(sql);
            createJobRequest.setProperties(properties);//无，则不用设置。
            //clusterId和queueName可以不配置。如果不配置，则随机从项目绑定的cluster和queue中选择一个。
            createJobRequest.setClusterId(clusterId);
            createJobRequest.setQueueName(queueName);
            //engineVersion可以不配置。如果不配置，则使用current版本（推荐版本）。
            createJobRequest.setEngineVersion(engineVersion);
            createJobRequest.setDescription("test");
            createJobRequest.setPackages(packageName);//多个package之间用英文逗号分隔。
            createJobRequest.setJobType("flink_stream");//flink_stream/flink_batch大小写不敏感。
            createJobRequest.setApiType("sql");//datastream/sql大小写不敏感。
            createJobRequest.setFolderId(folderId);

            CreateJobResponse createJobResponse = PublicSample.getResponse(client, createJobRequest);
            System.out.println(new Gson().toJson(createJobResponse));
        } else {
            //修改作业。
            UpdateJobRequest updateJobRequest = new UpdateJobRequest();
            updateJobRequest.setProjectName(projectName);
            updateJobRequest.setJobName(jobName);
            updateJobRequest.setCode(sql);//设置需要修改的字段，不修改的字段不需要设置。

            UpdateJobResponse updateJobResponse = PublicSample.getResponse(client, updateJobRequest);
            System.out.println(new Gson().toJson(updateJobResponse));
        }

        //返回引用指定package的job，该接口查询上线后的job引用关系，未上线的job不会返回。
        GetRefPackageJobRequest getRefPackageJobRequest = new GetRefPackageJobRequest();
        getRefPackageJobRequest.setProjectName(projectName);
        getRefPackageJobRequest.setPackageName(packageName);
        GetRefPackageJobResponse getRefPackageJobResponse = PublicSample.getResponse(client, getRefPackageJobRequest);
        System.out.println(new Gson().toJson(getRefPackageJobResponse));

        //校验job是否存在语法错误。
        ValidateJobRequest validateJobRequest = new ValidateJobRequest();
        validateJobRequest.setProjectName(projectName);
        validateJobRequest.setJobName(jobName);
        ValidateJobResponse validateJobResponse = PublicSample.getResponse(client, validateJobRequest);
        System.out.println(new Gson().toJson(validateJobResponse));

        //获取job的planjson。该接口是异步接口，提交完成之后，需要调用CheckRawPlanJson接口获取结果:Session in run/success/fail。
        GetRawPlanJsonRequest getRawPlanJsonRequest = new GetRawPlanJsonRequest();
        getRawPlanJsonRequest.setProjectName(projectName);
        getRawPlanJsonRequest.setJobName(jobName);
        getRawPlanJsonRequest.setAutoconfEnable(true);//可以不填，默认为false。
        //设置这个job预期使用多少资源来运行，这个参数会影响到生成plan的大小，不提供的话，底层引擎会生成默认资源配置。
        getRawPlanJsonRequest.setExpectedCore(2f);//这个参数与下面的参数可以同时提供，也可以同时不提供，不允许一个提供，一个不提供。
        getRawPlanJsonRequest.setExpectedGB(9f);
        GetRawPlanJsonResponse getRawPlanJsonResponse = PublicSample.getResponse(client, getRawPlanJsonRequest);

        CheckRawPlanJsonRequest checkRawPlanJsonRequest = new CheckRawPlanJsonRequest();
        checkRawPlanJsonRequest.setProjectName(projectName);
        checkRawPlanJsonRequest.setJobName(jobName);
        checkRawPlanJsonRequest.setSessionId(getRawPlanJsonResponse.getSessionId());

        String planJson;
        CheckRawPlanJsonResponse checkRawPlanJsonResponse;
        while (true) {
            checkRawPlanJsonResponse = PublicSample.getResponse(client, checkRawPlanJsonRequest);
            System.out.println(checkRawPlanJsonResponse.getPlanJsonInfo().getStatus());
            if (checkRawPlanJsonResponse.getPlanJsonInfo().getStatus().equals("success")) {
                planJson = checkRawPlanJsonResponse.getPlanJsonInfo().getPlanJson();
                break;
            } else if (checkRawPlanJsonResponse.getPlanJsonInfo().getStatus().equals("fail")) {
                System.out.println(checkRawPlanJsonResponse.getPlanJsonInfo().getErrorMessage());
                return;
            }
            Thread.sleep(1000);//每秒查询一次。
        }

        //修改作业planjson。
        UpdateJobRequest updateJobRequest = new UpdateJobRequest();
        updateJobRequest.setProjectName(projectName);
        updateJobRequest.setJobName(jobName);
        updateJobRequest.setPlanJson(planJson);
        UpdateJobResponse updateJobResponse = PublicSample.getResponse(client, updateJobRequest);
        System.out.println(new Gson().toJson(updateJobResponse));

        //预估当前job所配置的planjson需要的资源数，为上线接口需要提供的maxCU数值提供参考，仅blink-3.2.1及以上版本支持该功能。
        CalcPlanJsonResourceRequest calcPlanJsonResourceRequest = new CalcPlanJsonResourceRequest();
        calcPlanJsonResourceRequest.setProjectName(projectName);
        calcPlanJsonResourceRequest.setJobName(jobName);
        CalcPlanJsonResourceResponse calcPlanJsonResourceResponse = PublicSample.getResponse(client, calcPlanJsonResourceRequest);
        System.out.println(new Gson().toJson(calcPlanJsonResourceResponse));

        //获取作业上一次运行记录的最后一次自动调优的planjson，注意:是上一次的运行记录，包括停止的和暂停的。如果不存在，则返回null。
        GetJobLatestAutoScalePlanRequest getJobLatestAutoScalePlanRequest = new GetJobLatestAutoScalePlanRequest();
        getJobLatestAutoScalePlanRequest.setProjectName(projectName);
        getJobLatestAutoScalePlanRequest.setJobName(jobName);
        GetJobLatestAutoScalePlanResponse getJobLatestAutoScalePlanResponse = PublicSample.getResponse(client, getJobLatestAutoScalePlanRequest);
        System.out.println(new Gson().toJson(getJobLatestAutoScalePlanResponse));

        //上线作业。
        CommitJobRequest commitJobRequest = new CommitJobRequest();
        commitJobRequest.setProjectName(projectName);
        commitJobRequest.setJobName(jobName);
        commitJobRequest.setIsOnOff(true);//是否开启autoscale自动调优。不填，表示不开启，仅blink-3.2.1及以上版本支持该功能。
        commitJobRequest.setMaxCU(100);//设置最大资源使用上限。
        commitJobRequest.setConfigure("{\"fetch_delay\":44.5}");//json格式，当前仅支持这一个配置项，大小写敏感，value是double型。
        CommitJobResponse commitJobResponse = PublicSample.getResponse(client, commitJobRequest);
        System.out.println(new Gson().toJson(commitJobResponse));

        //获取实例。
        GetInstanceRequest getInstanceRequest = new GetInstanceRequest();
        getInstanceRequest.setProjectName(projectName);
        getInstanceRequest.setJobName(jobName);

        //-1表示获取流作业的当前运行实例（因为流作业某一时刻最多只会存在一个示例）。注意：批作业需要填实际的运行实例id。
        getInstanceRequest.setInstanceId(-1L);
        GetInstanceResponse getInstanceResponse = PublicSample.getResponse(client, getInstanceRequest);
        System.out.println(new Gson().toJson(getInstanceResponse));

        GetInstanceResponse.Instance instance = getInstanceResponse.getInstance();

        {
            //启动作业。
            if (instance == null || "UNKNOWN".equals(instance.getActualState()) || "TERMINATED".equals(instance.getActualState())) {
                StartJobRequest startJobRequest = new StartJobRequest();
                startJobRequest.setProjectName(projectName);
                startJobRequest.setJobName(jobName);

                Map map = new HashMap<String, String>();
                //startOffset表示启动点位。
                map.put("startOffset", String.valueOf(System.currentTimeMillis()));//精确到ms时间戳。
                startJobRequest.setParameterJson(new Gson().toJson(map));//json格式参数。
                StartJobResponse startJobResponse = PublicSample.getResponse(client, startJobRequest);
                System.out.println(new Gson().toJson(startJobResponse));

                //等待启动成功。
                while (true) {
                    GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("WAITING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            // 作业启动成功后，可以调用如下的接口进行运维。
            //获取instance的运行dag图。
            GetInstanceDetailRequest getInstanceDetailRequest = new GetInstanceDetailRequest();
            getInstanceDetailRequest.setProjectName(projectName);
            getInstanceDetailRequest.setJobName(jobName);
            getInstanceDetailRequest.setInstanceId(-1L);
            GetInstanceDetailResponse getInstanceDetailResponse = PublicSample.getResponse(client, getInstanceDetailRequest);
            System.out.println(new Gson().toJson(getInstanceDetailResponse));

            //获取instance的运行时配置。
            GetInstanceConfigRequest getInstanceConfigRequest = new GetInstanceConfigRequest();
            getInstanceConfigRequest.setProjectName(projectName);
            getInstanceConfigRequest.setJobName(jobName);
            getInstanceConfigRequest.setInstanceId(-1L);
            GetInstanceConfigResponse getInstanceConfigResponse = PublicSample.getResponse(client, getInstanceConfigRequest);
            System.out.println(new Gson().toJson(getInstanceConfigResponse));

            //获取instance的运行时checkpoint信息。
            GetInstanceCheckpointRequest getInstanceCheckpointRequest = new GetInstanceCheckpointRequest();
            getInstanceCheckpointRequest.setProjectName(projectName);
            getInstanceCheckpointRequest.setJobName(jobName);
            getInstanceCheckpointRequest.setInstanceId(-1L);
            GetInstanceCheckpointResponse getInstanceCheckpointResponse = PublicSample.getResponse(client, getInstanceCheckpointRequest);
            System.out.println(new Gson().toJson(getInstanceCheckpointResponse));

            //获取instance的运行时异常信息(failover信息）。
            GetInstanceExceptionsRequest getInstanceExceptionsRequest = new GetInstanceExceptionsRequest();
            getInstanceExceptionsRequest.setProjectName(projectName);
            getInstanceExceptionsRequest.setJobName(jobName);
            getInstanceExceptionsRequest.setInstanceId(-1L);
            GetInstanceExceptionsResponse getInstanceExceptionsResponse = PublicSample.getResponse(client, getInstanceExceptionsRequest);
            System.out.println(new Gson().toJson(getInstanceExceptionsResponse));

            //如果上线时作业开启了自动调优，调优效果不理想，可中途关闭自动调优，关闭之后也可以再次打开。
            UpdateAutoScaleConfigRequest updateAutoScaleConfigRequest = new UpdateAutoScaleConfigRequest();
            updateAutoScaleConfigRequest.setProjectName(projectName);
            updateAutoScaleConfigRequest.setJobName(jobName);
            updateAutoScaleConfigRequest.setInstanceId(-1L);
            updateAutoScaleConfigRequest.setConfigJson("{\"autoscale.enable\":false}");//json格式，当前只支持这个key，大小写敏感，取值为true或者false。
            UpdateAutoScaleConfigResponse updateAutoScaleConfigResponse = PublicSample.getResponse(client, updateAutoScaleConfigRequest);
            System.out.println(new Gson().toJson(updateAutoScaleConfigResponse));

            //查询instance的运行时各项指标的数据曲线信息。
            //当前只支持查询最近两小时的曲线，metric的格式为：blink.{projectName}.{jobName}.{metricName}。
            String metricJson = "{\n" +
                    "\t\"start\": 1547637620000,\n" + //精确到ms时间戳。
                    "\t\"limit\": \"avg:sample:50\",\n" +
                    "\t\"end\": 1547638420000,\n" +
                    "\t\"queries\": [{\n" +
                    "\t\t\"downsample\": \"20s-avg\",\n" +
                    "\t\t\"metric\": \"blink.bayes_team.huayuan_test_job.delay\",\n" +
                    "\t\t\"granularity\": \"20s\",\n" +
                    "\t\t\"aggregator\": \"max\"\n" +
                    "\t}, {\n" +
                    "\t\t\"downsample\": \"20s-avg\",\n" +
                    "\t\t\"metric\": \"blink.bayes_team.huayuan_test_job.fetched_delay\",\n" +
                    "\t\t\"granularity\": \"20s\",\n" +
                    "\t\t\"aggregator\": \"max\"\n" +
                    "\t}]\n" +
                    "}";

            GetInstanceMetricRequest getInstanceMetricRequest = new GetInstanceMetricRequest();
            getInstanceMetricRequest.setProjectName(projectName);
            getInstanceMetricRequest.setJobName(jobName);
            getInstanceMetricRequest.setInstanceId(-1L);
            getInstanceMetricRequest.setMetricJson(metricJson);
            GetInstanceMetricResponse getInstanceMetricResponse = PublicSample.getResponse(client, getInstanceMetricRequest);
            System.out.println(new Gson().toJson(getInstanceMetricResponse.getMetrics()));

            //获取instance实际使用的资源信息（cpu/memory）。
            GetInstanceResourceRequest getInstanceResourceRequest = new GetInstanceResourceRequest();
            getInstanceResourceRequest.setProjectName(projectName);
            getInstanceResourceRequest.setJobName(jobName);
            getInstanceResourceRequest.setInstanceId(-1L);
            GetInstanceResourceResponse getInstanceResourceResponse = PublicSample.getResponse(client, getInstanceResourceRequest);
            System.out.println(new Gson().toJson(getInstanceResourceResponse.getResource()));

            //批量获取多个作业的运行时状态。
            BatchGetInstanceRunSummaryRequest batchGetInstanceRunSummaryRequest = new BatchGetInstanceRunSummaryRequest();
            batchGetInstanceRunSummaryRequest.setProjectName(projectName);
            batchGetInstanceRunSummaryRequest.setJobType("flink_stream");
            batchGetInstanceRunSummaryRequest.setJobNames("job1,job2,job3");//多个job之间用英文逗号分隔，请确保都是stream job，并且job已存在且已上线，否则会忽略错误的job。
            BatchGetInstanceRunSummaryResponse batchGetInstanceRunSummaryResponse = PublicSample.getResponse(client, batchGetInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(batchGetInstanceRunSummaryResponse.getRunSummarys()));
        }

        {
            //暂停作业。
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("PAUSED");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待暂停成功。
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            //恢复作业。
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("PAUSED".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("RUNNING");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待恢复成功。
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("PAUSED".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            //停止作业。
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("TERMINATED");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待停止成功。
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        //下线作业。
        OfflineJobRequest offlineJobRequest = new OfflineJobRequest();
        offlineJobRequest.setProjectName(projectName);
        offlineJobRequest.setJobName(jobName);
        OfflineJobResponse offlineJobResponse = PublicSample.getResponse(client, offlineJobRequest);
        System.out.println(new Gson().toJson(offlineJobResponse));

        //删除作业。
        DeleteJobRequest deleteJobRequest = new DeleteJobRequest();
        deleteJobRequest.setProjectName(projectName);
        deleteJobRequest.setJobName(jobName);
        DeleteJobResponse deleteJobResponse = PublicSample.getResponse(client, deleteJobRequest);
        System.out.println(new Gson().toJson(deleteJobResponse));

        //删除package。
        DeletePackageRequest deletePackageRequest = new DeletePackageRequest();
        deletePackageRequest.setProjectName(projectName);
        deletePackageRequest.setPackageName(packageName);
        DeletePackageResponse deletePackageResponse = PublicSample.getResponse(client, deletePackageRequest);
        System.out.println(new Gson().toJson(deletePackageResponse));
    }
}
```

