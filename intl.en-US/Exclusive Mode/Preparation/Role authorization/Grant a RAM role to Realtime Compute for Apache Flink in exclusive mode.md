---
keyword: [automated role authorization, view the authorization information of the current role, add a permission policy]
---

# Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode

This topic describes how to grant a Resource Access Management \(RAM\) role to Realtime Compute for Apache Flink in exclusive mode.

## Procedure of automated RAM role authorization

A RAM role must be granted for your account before you use Realtime Compute for Apache Flink.

1.  Click **Authorize** to go to the authorization page.

    **Note:** The preceding message appears only when you have not properly granted the default RAM role to the service account of Realtime Compute for Apache Flink in exclusive mode.

2.  Click **AliyunStreamDefaultRole** and click **Authorize** to grant the default RAM role to Realtime Compute for Apache Flink.

    **Note:** After the RAM role is granted, refresh the page in the Realtime Compute for Apache Flink console. Then, you can perform service operations.


## View the authorization information of the current role

1.  Log on to the RAM console.

    -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
    -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
2.  In the left-side navigation pane, click RAM Roles. On the page that appears, click **AliyunStreamDefaultRole** in the **RAM Role Name** column.

3.  On the AliyunStreamDefaultRole page, click **AliyunStreamRolePolicy** on the **Permissions** tab.

4.  On the **Policy Document** tab, view the current policy information of Realtime Compute for Apache Flink.

    ```
    {
      "Version": "1",
      "Statement": [
        {
          "Action": [
            "ots:List*",
            "ots:DescribeTable",
            "ots:Get*",
            "ots:*Row"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "dhs:Create*",
            "dhs:List*",
            "dhs:Get*",
            "dhs:PutRecords",
            "dhs:DeleteTopic"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "log:List*",
            "log:Get*",
            "log:Post*"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "mns:List*",
            "mns:Get*",
            "mns:Send*",
            "mns:Publish*",
            "mns:Subscribe"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "drds:DescribeDrdsInstance",
            "drds:ModifyDrdsIpWhiteList"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "rds:Describe*",
            "rds:ModifySecurityIps*"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "vpc:DescribeVpcs",
            "vpc:DescribeVSwitches"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "ecs:CreateSecurityGroup",
            "ecs:AuthorizeSecurityGroup",
            "ecs:CreateNetworkInterface",
            "ecs:DescribeNetworkInterfaces",
            "ecs:AttachNetworkInterface",
            "ecs:DescribeNetworkInterfacePermissions",
            "ecs:CreateNetworkInterfacePermission"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": "oss:*",
          "Resource": "*",
          "Effect": "Allow"
        }
      ]
    }
    ```


## Add a permission policy

After you create a RAM role, you can add a specified permission policy to the RAM role.

1.  Log on to the RAM console.

    -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
    -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
2.  In the left-side navigation pane, choose **Permissions** \> **Policies**.

3.  Click **Create Policy**.

4.  On the page that appears, specify **Policy Name** and **Note**. **AliyunStreamDefaultRolePolicy** is used as the policy name in this example.

5.  Set **Configuration Mode** to **Visualized** or **Script** as required.

    -   If you select **Visualized**, click **Add Statement**. In the Add Statement pane, specify Permission Effect, Actions, and Resources.
    -   If you select **Script**, edit the policy script based on the [policy structure and syntax](/intl.en-US/Policy Management/Policy language/Policy structure and syntax.md).
6.  In the code box under **Policy Document**, enter the following code and click **OK**:

    ```
    {
      "Version": "1",
      "Statement": [
        {
          "Action": [
            "vpc:DescribeVpcs",
            "vpc:DescribeVSwitches"
          ],
          "Resource": "*",
          "Effect": "Allow"
        },
        {
          "Action": [
            "ecs:CreateSecurityGroup",
            "ecs:AuthorizeSecurityGroup",
            "ecs:CreateNetworkInterface",
            "ecs:DescribeNetworkInterfaces",
            "ecs:AttachNetworkInterface",
            "ecs:DescribeNetworkInterfacePermissions",
            "ecs:CreateNetworkInterfacePermission"
          ],
          "Resource": "*",
          "Effect": "Allow"
        }
      ]
    }                    
    ```

    **Note:** You can delete the following permissions after you create a cluster:

    -   ecs:CreateSecurityGroup
    -   ecs:AuthorizeSecurityGroup
7.  In the left-side navigation pane, click RAM Roles. On the page that appears, find **AliyunStreamDefaultRole** in the RAM role list and click **Add Permissions** in the **Actions** column.

8.  In the Add Permissions pane, click **Custom Policy** in the **Select Policy** section, and enter AliyunOSSFullAccess in the search box under Custom Policy.

9.  Click **AliyunOSSFullAccess** in the **Authorization Policy Name** column.

10. In the **Select Policy** section, click **Custom Policy**.

11. In the search box under **Custom Policy** of the **Select Policy** section, enter AliyunStreamDefaultRolePolicy.

12. Click **AliyunStreamDefaultRolePolicy** in the **Authorization Policy Name** column.

13. Click **OK**.


