# Monitoring and alerting {#concept_66088_zh .concept}

This topic describes monitoring and alerting in Realtime Compute, and how to create and start alert rules.

## Background {#section_d1v_prs_bgb .section}

Realtime Compute provides data processing capabilities for CloudMonitor, so that you can monitor the health of your job in real time. CloudMonitor collects monitoring metrics of Alibaba Cloud resources and your custom metrics. It can be used to detect the availability of your services and allows you to set alerts for specific metrics. It allows you to be fully aware of resource usage, service status, and service health on Alibaba Cloud. It also enables you to promptly respond to error alerts and ensure smooth running of your application.

With Realtime Compute, you can specify alerts for the following performance metrics:

-   Processing delay
-   Input RPS
-   Output RPS
-   Failover rate
-   Data pending time

## Operations {#section_flh_srs_bgb .section}

1.  Log on to CloudMonitor. On the Administration page of Realtime Compute, click **Monitor** to redirect to CloudMonitor.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873734026_en-US.png)

2.  View Realtime Compute monitoring alerts.
    1.  Select a project that you want to monitor, and click **ViewJob**.
    2.  On the page that appears, click **Monitoring Charts** in the Actions column of a job. 

        **Note:** If you have not set any alert rules, use either of the following methods to enter the alert rule creation page:

        1.  Select a job that you want to set alerts for, and click **Set Alert Rules**.
        2.  On the **Alert Rules** tab, click **here** or **Create Alert Rule**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834029_en-US.png)


## Create alert rules {#section_wmq_css_bgb .section}

To create alert rules, follow these steps:

1.  In the Related Resource section, set Products to Stream Computing \(which is the old version of Realtime Compute\) and Resource Range to Project, select a project, and select one or more jobs from the Job drop-down list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834030_en-US.png)

2.  Set alert rules.

    The following table lists the supported types of alert rules.

    |Rule|Unit|
    |----|----|
    |Processing delay|Seconds|
    |Input RPS|Entries|
    |Output RPS|Entries|
    |Failover rate|Failover times per second **Note:** 

    -   The failover rate indicates the average number of failover times per second in a period of time. Assume that a failover occurred in the last minute. The failover rate in the last minute is 1/60 = 0.01667 = 1.667%.
    -   When you specify the failover rate threshold, enter a percentage value.
 |
    |Data pending time|Seconds|

    The following figure shows the recommended parameter settings.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834031_en-US.png)

3.  Configure notification methods.
    -   Notification contacts

        You can click **Quickly create a contact group** in the **Notification Contact** area, or add other people to an existing contact group.

        To add new alert contacts, follow these steps:

        1.  In the **Notification Method** section, click **Quickly create a contact group**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834032_en-US.png)

        2.  In the **Add Contact Group** dialog box that appears, click **Create Alert Contact**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834033_en-US.png)

        3.  Configure the alert contact information.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834034_en-US.png)

    -   Notification methods

        -   Mobile phone + email + TradeManager + DingTalk Chatbot
        -   Email + TradeManager + DingTalk Chatbot
        -   Alert callback
        You can configure the notification methods based on your actual business requirements.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41075/156498873834035_en-US.png)


