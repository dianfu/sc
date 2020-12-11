# 开源自建监控组件（ACK）

实时计算Flink半托管/ACK兼容开源自建监控组件Prometheus。

## 开通服务

1.  登录[Flink半托管/ACK控制台](https://realtime-compute.console.aliyun.com/console/k8s/cn-shanghai)。

2.  在**Flink半托管/ACK**页签对应集群操作列，单击**组件**后，然后单击**添加/修改组件**，添加报警组件。

    **说明：** 自建报警仅适用于vvp-2.1.0及以上版本，如果当前版本为vvp-1.5.1，请升级到vvp-2.1.0。

    ![Flink云原生控制台](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75378.png)

3.  登录[阿里云ACK控制台](https://cs.console.aliyun.com/#/k8s/pod/list)后，进入Prometheus pod内部控制台。

    ![阿里云ACK控制台](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75379.png)

    Prometheus pod内部控制台界面如下图所示。

    ![Prometheus pod内部控制台](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75380.png)

4.  在Prometheus pod内部控制台上，输入pwd，确认当前路径为/Prometheus。

5.  在Prometheus pod内部控制台上，输入id，确认当前登录用户信息为**uid=65534\(nobody\) gid=65534\(nogroup\) groups=65534\(nogroup\)**。

6.  创建文件夹rules、文件alerting rules（yaml格式）和文件recording rules（yaml格式）。

    ```
    # 进入默认目录。
    cd /prometheus
    # 创建rules文件夹。
    mkdir rules
    cd rules
    # 创建alerting rules file和recording rules file。
    # alerting rules file需要符合alerting_rule*.yaml的格式。
    # recording rules file需要符合recording_rule*.yaml的格式。
    touch alerting_rules-flink-job.yaml
    touch recoding_rules-flink-job.yaml
    ```

7.  填写alerting rules和recording rules文件内容。

    alerting rules和recording rule语法规则相同。

    ```
    groups:
      [ - <rule_group> ]
    ```

    示例如下：

    -   alerting rules

        ```
        groups:
        - name: example
          rules:
          - alert: HighRequestLatency
            expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
            for: 10m
            labels:
              severity: page
            annotations:
              summary: High request latency
        ```

    -   recording rule

        ```
        groups:
          - name: example
            rules:
            - record: job:http_inprogress_requests:sum
              expr: sum(http_inprogress_requests) by (job)
        ```

8.  执行以下命令加载prometheus的报警规则的配置信息。

    ```
    # 发送SIGHUB信号。
    kill -1 1
    ```

9.  查看更新后的报警规则信息。

    ![prometheus网址](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75390.png)


## 配置钉钉报警

目前仅支持通过钉钉报警。

1.  在钉钉群中[添加机器人](https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq)。

2.  安全设置完成后，复制机器人的Webhook地址。格式为https://oapi.dingtalk.com/robot/send?access\_token=XXXXXX。

3.  在阿里云ACK控制台，找到vvp-xxx-dingtalk-alert-gateway的**Deployment**，选择**更多** \> **查看YAML**。

    ![查看YAML](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75441.png)

4.  在弹出的YAML文本框中找到**containers**的**args**信息。

    ![args信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2660737951/p75442.png)

5.  将access\_token=替换为步骤二获得的token后，单击**更新**。

    如果有报警产生，可在钉钉内收到消息。

    ![钉钉消息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3660737951/p75444.png)


