# Create cloud disk data volumes {#concept_shk_smt_xdb .concept}

Cloud disk is a block storage system officially provided by Alibaba Cloud, and an elastic block storage product of distributed storage architecture that Alibaba Cloud provides to Elastic Compute Service \(ECS\). Cloud disk provides random storage of data block level, features in low latency, persistence, and high reliability, and adopts the distributed mechanism of three copies.

Cloud disk can be used for relational database applications or development and test applications. For more information, see [Elastic block storage](../../../../intl.en-US/Product Introduction/Block storage/Elastic block storage.md#).

## Limits {#section_m13_5mt_xdb .section}

-   The cloud disk and the ECS instances in the cluster must be in the same region and zone.
-   Cloud disk data volumes only support being mounted to a single machine, but does not support the shared mode.
-   A cloud disk data volume can be used by only one container at the same time.

## Prerequisites {#section_gmw_5mt_xdb .section}

-   Create a cloud disk manually in the ECS console before using the cloud disk data volume.
-   Upgrade your Agent to the latest version. For more information, see [Upgrade Agent](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade Agent.md#).
-   Deploy the acsvolumedriver application in the cluster. We recommend that you upgrade the acsvolumedriver application to the latest version.

    You can deploy and upgrade the acsvolumedriver application by upgrading the system services. For more information, see [Upgrade system services](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade system services.md#).


## Procedure {#section_bk4_vmt_xdb .section}

**Step 1 Create a cloud disk**

In this example, create a cloud disk that is in the same region and zone as the cluster.

1.  Log on to the  [ECS console](https://ecs.console.aliyun.com).
2.  Click **Cloud Disks** in the left-side navigation pane.
3.  On the Disk List page, click **Create Cloud Disk** in the upper-right corner.
4.  Configure the parameters for the cloud disk. Select the corresponding region and zone. Create the cloud disk according to the guidance on the page.

    **Note:** The purchased cloud disk can be mounted only when you select the same zone as the server. The cloud disk cannot be mounted across zones or regions.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7079/5068_en-US.png)


**Step 2 Create data volumes by using the cloud disk**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click **Data Volumes** in the left-side navigation pane.
3.  Select the cluster in which you want to create the data volume from the Cluster list and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7079/5069_en-US.png)

4.  In the displayed dialog box, select **Cloud Disk** as the **Type**, configure the data volume parameters and then click **Create**. Container Service will create a data volume with the same name on each cluster node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7079/5070_en-US.png)

    -   **Name**: The name of the data volume, The data volume name must be unique within the cluster.
    -   **Cloud Disk ID**: Select the cloud disk to be mounted and is in the same region and zone as the cluster.  In this example, select the ID of the cloud disk created in step 1.
    -   **AccessKey ID** and **AccessKey  Secret**: The AccessKey of your account.

        **Note:** Container Service begins to support the STS Token function since December 5, 2017. If your cluster is created after that, you must enter  the AccessKey when you create a cloud disk data volume in the cluster.

    -   File System Type: You can select the data type in which data is stored to the cloud disk. The supported types include ext4, ext3, xfs, and vfat.

After the data volume is successfully created, you can view the cloud disk data volume on the Data Volume List page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7079/5071_en-US.png)

## Subsequent operations {#section_pzx_tmt_xdb .section}

You can manage the cloud disk data volumes, including deleting all the data volumes with the same name and viewing data volume parameters.

