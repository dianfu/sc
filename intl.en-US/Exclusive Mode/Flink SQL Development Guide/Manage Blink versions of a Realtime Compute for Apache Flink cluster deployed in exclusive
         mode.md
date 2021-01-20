# Manage Blink versions of a Realtime Compute for Apache Flink cluster deployed in exclusive mode

Realtime Compute for Apache Flink provides the version management feature for you to manage Blink versions of a Realtime Compute for Apache Flink cluster in exclusive mode.

## Install a version

1.  Go to the Version Management page

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

    2.  Move the pointer over the username in the top navigation bar and click **Project Management**.

    3.  In the left-side navigation pane, choose **Cluster Management** \> **Clusters**.

    4.  On the **Clusters** page, find the cluster for which you want to manage Blink versions. In the **Actions** column, choose **More** \> **Version Management**.

2.  On the **Installable Version** tab, find the version you want to install, and click **Install** in the **Actions** column.

    On the **Installable Version** tab, choose a proper version based on your business requirements and the version features.

    |Tag|Description|
    |---|-----------|
    |**stable**|The recommended stable Blink version.|
    |**beta**|The beta version programmed for testing.**Note:** We recommend that you install a beta version in only specified scenarios. The performance and the stability of the beta version cannot be ensured in other scenarios. |
    |No tag|The historical stable version.|

    **Note:**

    -   You can install one version at a time. During installation, the cluster status is **Version Installing**.
    -   You cannot install a version that has been installed.
    -   Each version is configured with a service period. The default service period is one year. After the service period expires, you can continue to use the version, but Alibaba Cloud no longer maintains the version.

## Switch a version

Blink features vary based on Blink versions. You can configure a Blink version based on your business requirements. You can **switch the Blink version** for a job.

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

2.  In the top navigation bar, click **Development**.

3.  On the **Development** page, double-click the job in a folder that stores the job to go to the **job editing** page.

4.  In the right side of the **job editing** page, click Version Information.

5.  From the version list, select the Blink version you want to switch to.


## Uninstall a Blink version

Realtime Compute for Apache Flink allows you to install only three Blink versions. If you already install three Blink versions and need to install a new version, you must uninstall an existing version.

1.  Go to the Version Management page

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

    2.  Move the pointer over the username in the top navigation bar and click **Project Management**.

    3.  In the left-side navigation pane, choose **Cluster Management** \> **Clusters**.

    4.  On the **Clusters** page, find the cluster for which you want to uninstall a version. In the **Actions** column, choose **More** \> **Version Management**.

2.  On the **Installed Versions** tab, find the version, and click **Uninstall** in the **Actions** column.

    **Note:**

    -   You cannot uninstall a Blink version that is set to **current**.
    -   You cannot uninstall a Blink version that is referenced by a Realtime Compute for Apache Flink job.

## Specify the current version

You can select an installed Blink version and set the version to the current version of a job in the current cluster. Blink features vary based on Blink versions. You can set a Blink version to the current version of a Realtime Compute for Apache Flink cluster based on your business requirements.

1.  Go to the Version Management page

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

    2.  Move the pointer over the username in the top navigation bar and click **Project Management**.

    3.  In the left-side navigation pane, choose **Cluster Management** \> **Clusters**.

    4.  On the **Clusters** page, find the cluster for which you want to set a version to the current version. In the **Actions** column, and choose **More** \> **Version Management**.

2.  On the **Installed Version** page, find the version that you want to set to the current version, and click **Set to current** in the **Actions** column.


## FAQ

-   What can I do if the `blink-<version> already installed` error message appears during installation?

    The error message is returned because the Blink version has been installed. You do not need to install the version again.

-   What can I do if the `Flink versions exceeded max limitation:3` error message appears during installation?

    The error message is returned because the number of versions exceeds the upper limit. To install a new version, you must delete existing versions until the number of installed versions is less than three.

-   What can I do if the `Node:<nodeName> in project:<projectName> still ref the version:<blinkVersion>` error message appears during uninstallation?

    The error message is returned because the Blink version is referenced by an online job. You must bring the job offline based on the job name and project name provided in the error message.

-   What can I do if the following message appears when I click **Syntax Check** or **Publish**?

    ```
    code:[30006], brief info:[blink script not exist, please check blink version], context info:[blink script:[/home/admin/blink/blink-2.2.6-hotfix0/bin/flink], blink version:[/home/admin/blink/blink-2.2.6-hotfix0/bin/flink]]
    ```

    The error message is returned because the Blink version used by the job does not exist. In the Realtime Compute for Apache Flink console, choose **Version Management** \> **Installed Versions** to check whether the version exists.

    -   If the version does not exist, switch to another version or install the version.
    -   If the version exists, [submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).

