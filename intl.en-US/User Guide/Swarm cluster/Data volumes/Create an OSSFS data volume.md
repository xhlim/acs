# Create an OSSFS data volume {#concept_nsx_5lr_xdb .concept}

OSSFS is a FUSE-based file system provided by Alibaba Cloud \(click [https://github.com/aliyun/ossfs](https://github.com/aliyun/ossfs) to view the project homepage\).  OSSFS data volumes can package Object Storage Service \(OSS\) buckets as data volumes.

The performance and functions of OSSFS differ from those of local file systems because data must be synchronized to the cloud by the means of network. Do not run databases, I/O-intensive applications, logs and other applications that require constantly writing files to OSSFS. OSSFS is suitable for sharing configuration files across containers, uploading attachments, and other scenarios without rewrite operations.

**OSSFS differs from local file systems in the following ways:**

-   Random write or append write leads to the entire file being overwritten.
-   Metadata operations, such as list directory, provide poor performance because the system must remotely access the OSS server.
-   The file/folder rename operation is not atomic.
-   Coordinate the actions of each client on your own when multiple clients are mounted to the same OSS bucket. For example, avoid multiple clients from writing the same file.
-   Hard link is not supported.

## Prerequisites {#section_a5g_yjt_xdb .section}

You can only use the data volume function when your cluster meets the following conditions:

-   The cluster Agent is of version 0.6 or later.

    You can check your Agent version on the Cluster List page. Select the target cluster, and click **More** \> **Upgrade Agent** on the right.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5050_en-US.png)

    If your Agent version is earlier than 0.6, upgrade the Agent first.  For how to upgrade Agent, see [Upgrade Agent](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade Agent.md#).

-   Deploy the acsvolumedriver application in the cluster. We recommend that you upgrade the acsvolumedriver application to the latest version.

    You can deploy and upgrade the acsvolumedriver application by upgrading the system services. For more information, see [Upgrade system services](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade system services.md#).

    **Note:** When acsvolumedriver is upgraded or restarted, containers  that use OSSFS data volumes are restarted, and your services are also restarted.


## Step 1. Create an OSS bucket {#section_d5g_yjt_xdb .section}

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).
2.  and create a bucket. 

    In this example, create a bucket in the region of China East 1 \(Hangzhou\).


## Step 2. Create an OSSFS data volume {#section_f5g_yjt_xdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click **Data Volumes** in the left-side navigation pane.
3.  Select the cluster in which you want to create a data volume from the Cluster list. Click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5052_en-US.png)

4.  The **Create Data Volume** dialog box appears. Select **OSS** as the Type, configure the data volume parameters, and then click **Create**.  Container Service creates a data volume with the same name on each cluster node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5053_en-US.png)

    -   **Name**: The name of the data volume that must be unique within the cluster.
    -   **Access Key ID**/**Access Key Secret**: The AccessKey required to access OSS. You can obtain them from the [AccessKey console](https://ak-console.aliyun.com/).
    -   **Bucket ID**: The name of the OSS bucket to be used. Click **Select Bucket** in the dialog box, and click **Select**.
    -   **Access domain name**: If the bucket and ECS instances are in different regions, select **external domain name**. If they are located in the same region, you must select the corresponding cluster network type. For VPC network, select **VPC domain name**, and for classic network, select **intranet domain name** respectively.
    -   **File Caching**: Select Disable if you want to synchronize the modifications of the same file on multiple machines \(for example, modify the file on machine A and read the modified contents on machine B\).

        **Note:** Turning off the File Caching causes ls folder to become slow, especially when a lot of files exist in the same folder. Therefore, when there is no such requirement, enable the File Caching and increase the speed of the ls command.


You can view the created OSSFS data volumes on the Data Volume List page.

## Subsequent operations {#section_m5g_yjt_xdb .section}

After the data volumes are created, you can use the data volumes created in your app. For more information about how to use data volumes in an application, see [Use third-party data volumes](intl.en-US/User Guide/Swarm cluster/Data volumes/Use third-party data volumes.md#).

