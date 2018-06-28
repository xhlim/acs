# Upgrade Docker daemon {#task_exd_mwl_xdbafaf .task}

Standard Docker daemon is installed on each server in the cluster to manage containers.

**Note:** 

-   The cluster Docker daemon upgrade requires that the machine is able to access the Internet to download necessary software packages.
-   The cluster Docker daemon upgrade may fail. To guarantee your data security, we recommend that you back up snapshots before upgrading Docker daemon.
-   During the cluster Docker daemon upgrade, the services deployed on the cluster are interrupted and you cannot perform operations on the cluster and applications. Make appropriate arrangements before the upgrade. The upgrade lasts 3‒30 minutes. The cluster status changes to Running after the upgrade.

You can view the Docker version of the cluster on the Cluster List page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4837_en-US.png)

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.   Under Swarm, click **Clusters** in the left-side navigation pane. 
3.   On the Cluster List page, click **Upgrade** in the Docker Version column, or click**More** \> **Upgrade Docker**at the right of the cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4840_en-US.png)

4.   On the Upgrade Docker page, click **Upgrade Agent** to upgrade the Agent first if your Agent is not in the latest version. 

     

5.   If your Agent is in the latest version, upgrade Docker daemon 

     

    in the following ways:

    -   Upgrade Directly

        Click **Upgrade Directly** to enter the Docker Engine upgrade process.

    -   Back up Snapshot before Upgrade

        We recommend that you back up the snapshots before upgrading Docker daemon. In this way, you can recover Docker daemon by using the snapshots if an error occurs during the upgrade process.

        Click **Back up Snapshot before Upgrade**, and then the system calls the Elastic Compute Service \(ECS\) API to take snapshots of the cluster nodes.

        Backing up snapshots may take some time. Wait until the snapshots are backed up, and then the system automatically enters the Docker Engine upgrade process.

        If the snapshots failed to be backed up, you can click **Continue** or **Quit**. Click **Continue** to enter the Docker Engine upgrade process, or click **Quit** to give up the upgrade.


Return to the Cluster List page and you can see that the cluster you upgraded the Docker daemon is in the `Docker-Engine is upgrading` status. This may take a while as container data will be backed up during the upgrade of the Docker Engine.

