# Environment preparations {#concept_svl_yt5_vdb .concept}

Before using the Alibaba Cloud Container Service blockchain solution, complete the corresponding environment preparations, which mainly includes:

-   Create a Kubernetes cluster
-   Bind an Elastic IP \(EIP\) to a worker node
-   Create a file system and add a mount point
-   Mount the file system to an Elastic Compute Service \(ECS\) instance of the Kubernetes cluster

This document introduces how to prepare the environment.

## Create a Kubernetes cluster {#section_lh2_355_vdb .section}

The blockchain solution is deployed based on a Kubernetes cluster built by ECS. For how to create a Kubernetes cluster, see [Create a cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#) . When creating a Kubernetes cluster, you must complete the following key configurations to make sure the blockchain solution can be deployed successfully.

-   Region: The Kubernetes cluster and the NAS file system must be in the same region. Make sure the selected region supports both the Kubernetes cluster and the NAS file system \(intersection of both\). For the list of regions that support the NAS file system, see **Products** \> **Storage & CDN** \> **NAS** \> ** File System List**.
-   Network type: Select Virtual Private Cloud \(VPC\).
-   SSH login: For ease of management, select the **Enable SSH access for Internet** check box.
-   Node configurations: We recommend that you use the default settings \(such as three master nodes and three worker nodes\). You can also configure the nodes as per your needs. The numbers of softwares, services, and containers deployed by the blockchain network are large. Therefore, make sure the cluster resources can meet the requirements.

Click Create Cluster. Wait some time \(for example, several minutes, which depends on the number of ECS instances\) until the Kubernetes cluster and the ECS instances are created.

After the Kubernetes cluster is created, click **Manage** at the right of the created cluster on the Cluster List page. In the **Basic Information** \> **Connection Information** section, record the Master node SSH IP address, which is an Internet address and used as the external address.

Click **Kubernetes** \> **Nodes**in the left-side navigation pane. Record the IP addresses \(intranet addresses\) of the cluster nodes \(both master nodes and worker nodes\) for later usage.

## Bind an EIP to a worker node {#section_ph2_355_vdb .section}

This section is a prerequisite for the access to the blockchain network from outside the cluster, which creates and binds an EIP to a worker node of the Kubernetes cluster.

**Procedure**

1.  Log on to the [EIP console](https://partners-intl.console.aliyun.com/#/ip).
2.  Click **Elastic IP Addresses** in the left-side navigation pane.
3.  Click **Create EIP**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/2641_en-US.png)

4.  Select the Region in which the Kubernetes cluster resides. Complete the other configurations as per your needs. Click **Buy Now**.
5.  After activating the EIP, wait until the purchased EIP is displayed in the list and then click **Bind** at the right of the EIP.
6.  The Bind Elastic IP Address page appears. Select ECS Instance from the Instance Type drop-down list and select a worker node \(the instance name begins with node, instead of master\) from the ECS Instance drop-down list. **OK**.
7.  After binding the EIP to the worker node, record the IP address of the purchased EIP on the Elastic IP Addresses page.

## Create a file system and add a mount point {#section_vh2_355_vdb .section}

In the blockchain solution, a file system is mainly used to store and distribute the certificates, keys, and configurations of the blockchain and persistently store the data of the blockchain master nodes.

**Note:** The usage of a file system only applies to the development and test phases of blockchain applications and solutions. For deployment in the production environment, contact us to further discuss specific business and technical needs and determine the most suitable method together.

For how to create a file system, see [Create a file system](https://www.alibabacloud.com/help/zh/faq-detail/27526.htm) . When creating a file system, you must complete the following key configurations to make sure the blockchain solution can be deployed successfully.

-   Region: Select the region in which the container cluster resides.
-   Protocol Type: Select NFS.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/2642_en-US.png)


For how to add a mount point, see Add a mount point in a VPC in [Add a mount point](https://www.alibabacloud.com/help/zh/faq-detail/60431.htm).

After adding a mount point, click **Manage** at the right of the file system.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/2644_en-US.png)

Record the Mount Address of the mount point.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7393/2646_en-US.png)

## Mount a file system to ECS instances {#section_c32_355_vdb .section}

In the development and test environment of blockchain, the shared file system is used to distribute the dynamically generated certificates, keys, and configurations of the blockchain to each blockchain node. For this purpose, you must run a command to mount the created file system to Kubernetes cluster nodes.

Use the root account to remotely log on to each worker node of the Kubernetes cluster to access applications and each master node of the Kubernetes cluster to delete the data directory of the blockchain network.

Use the root account to create the data directory /data, grant permissions, and mount an NFS file system.

**Procedure**

Log on to the Internet address of the master node by using SSH. Use the following command to log on to all the worker nodes and master nodes.

```
ssh root@<intranet address of the node>
```

Run the following command on the first node. Specify the NFS 4.0 protocol.

```
mkdir /data
mount -t nfs -o vers=4.0 035bb49518-pja63.cn-hangzhou.nas.aliyuncs.com:/ /data #Replace with your own mount address.
mkdir -p /data/fabric
chmod -R 777 /data/fabric
```

Run the following command on each of the other nodes. Specify the NFS 4.0 protocol.

```
mkdir /data
mount -t nfs -o vers=4.0 035bb49518-pja63.cn-hangzhou.nas.aliyuncs.com:/ /data #Replace with your own mount address.
```

**Note:** If a node is restarted, rerun the mount command to remount the file system.

