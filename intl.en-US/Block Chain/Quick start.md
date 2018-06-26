# Quick start {#concept_qqp_vqt_vdb .concept}

This document is a quick start guide for deploying the blockchain network from the beginning, in which most settings use the default or example values. For more information about the configurations, see the subsequent sections.

## Limits {#section_gny_zqt_vdb .section}

-   The container cluster and the NAS file system must be in the same region. Make sure the selected region supports both the Kubernetes cluster and the NAS file system \(intersection of both\). For the list of regions that support the NAS file system, see **Products** \> **Storage & CDN** \> **NAS** \> **File System List**.
-   The usage of a file system only applies to the development and test phases of blockchain applications and solutions. For deployment in the production environment, contact us to further discuss specific business and technical needs and determine the most suitable method together.

## Step 1 Create a Kubernetes cluster {#section_gln_lrt_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. Click **Create Kubernetes Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2535_en-US.png)

3.  Configure the basic information for the cluster. In this example, complete the configurations as follows:
    -   Enter the Cluster Name. For example, k8s-blockchain.
    -   Select **China East 1 \(Hangzhou\)** as the Region.
    -   Select **China East 1 Zone B** as the Zone.
    -   Select **Auto Create** to create a **Virtual Private Cloud \(VPC\)** together with the Kubernetes cluster, or Use Existing to use an existing VPC.
    -   Configure the **Logon Password** and **Confirm Password**.
    -   Configure the generation, family, type, and number of the Elastic Compute Service \(ECS\) instances. The blockchain network deployment is resource-intensive. Therefore, we recommend that you use the default configurations.
    -   Select the **Enable SSH access for Internet** check box.
    -   Click **Create Cluster**. Wait a few minutes until the cluster is successfully created.
4.  On the Cluster List page, click **Manage** at the right of the created cluster \(k8s-blockchain\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2536_en-US.png)

5.  On the Basic Information page of the cluster, record the Master node SSH IP address, which is an Internet address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2544_en-US.png)

6.  On the Node List page, record the IP addresses \(intranet addresses\) of the cluster nodes \(both master nodes and worker nodes\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2545_en-US.png)


## Step 2 Bind an EIP to a worker node {#section_ppn_s5t_vdb .section}

1.  Log on to the [Elastic IP Address \(EIP\) console](https://partners-intl.console.aliyun.com/#/ip).
2.  Click **Elastic IP Addresses** in the left-side navigation pane. Click **Create EIP**.
3.  Select the Region in which the cluster k8s-blockchain resides. Complete the other configurations as per your needs. Click **Buy Now**.
4.  After activating the EIP, wait until the purchased EIP is displayed in the list and then click **Bind** at the right of the EIP.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2546_en-US.png)

5.  The Bind Elastic IP Address page appears. Select ECS Instance from the Instance Type drop-down list and select a worker node \(the instance name begins with node\) from the ECS Instance drop-down list. Click **OK**.
6.  After binding the EIP to the worker node, record the IP address of the purchased EIP on the Elastic IP Addresses page.

## Step 3 Create a file system and add a mount point {#section_z3m_vwt_vdb .section}

1.  Log on to the [NAS console](https://partners-intl.console.aliyun.com/#/nas).
2.  Select **China East 1 \(Hangzhou\)** in the region list and click **Create File System**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2547_en-US.png)

3.  Complete the configurations in the displayed Create File System dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2548_en-US.png)

    -   **Region**: Select **China East 1 \(Hangzhou\)**. Select the region in which the container cluster resides.
    -   **Storage Type**: In this example, select **Capacity-type**.
    -   **Protocol Type**: Select **NFS**.
    -   **Zone**: Select **China East 1 Zone B**. Different zones in the same region are interconnected.
    -   Click **OK**.
4.  Click **Click to go** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2550_en-US.jpg)

5.  Configure the NAS storage package.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2551_en-US.png)

    -   **Storage Type**: Select **Capacity-Type**.
    -   **Order Duration**: In this example, select 1 month. Select the duration as per your needs.
    -   Click **Buy Now**.
6.  If **Use Existing** is selected in the VPC field when you create the Kubernetes cluster, skip this step. If **Auto Create** is selected in the VPC field when you create the Kubernetes cluster, go to the [VPC console](https://partners-intl.console.aliyun.com/#/vpc). Change the VPC instance name to one that is easy to identify, for example, blockchain\_huadong1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2552_en-US.png)

7.  Click **Add Mount Point** at the right of the created file system. Complete the configurations in the displayed Add Mount Point dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2553_en-US.png)

    -   **Mount Point Type**: Select **VPC**.
    -   **VPC:** Select the VPC used when creating the container cluster.
    -   **VSwitch**: Select the VSwitch used when creating the container cluster.
    -   **Permission Group**: Select **VPC default permission group \(allow all\)**.
    -   Click **OK**.
8.  On the File System List page, click **Manage** at the right of the file system. Record the **Mount Address** on the File System Details page. In this example, the mount address is 07e9649902-nat8.cn-hangzhou.nas.aliyuncs.com.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2557_en-US.png)


## Step 4 Mount a file system to ECS instances {#section_atp_zxt_vdb .section}

1.  Log on to the Internet address of the cluster master node by using SSH. Use the following command to log on to all the worker nodes and master nodes.

    ```
    ssh root@<intranet address of the node>
    ```

2.  Run the following command on the first ECS instance. Specify the NFS 4.0 protocol when mounting the file system.

    ```
    mkdir /data
    mount -t nfs -o vers=4.0 07e9649902-nat8.cn-hangzhou.nas.aliyuncs.com:/ /data #Replace with your own mount address.
    mkdir -p /data/fabric
    chmod -R 777 /data/fabric
    ```

3.  Run the following command on each of the other ECS instances. Specify the NFS 4.0 protocol when mounting the file system.

    ```
     mkdir /data
     mount -t nfs -o vers=4.0 07e9649902-nat8.cn-hangzhou.nas.aliyuncs.com:/ /data #Replace with your own mount address.
    ```


## Step 5 Configure and deploy blockchain network {#section_lc1_zqt_vdb .section}

1.  Under Kubernetes, click **Store** \> **App Catalog** in the left-side navigation pane. Click **ack-hyperledger-fabric**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2560_en-US.png)

2.  Select the cluster in which the blockchain network is to be deployed from the **Clusters** drop-down list and enter the **Release Name**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2561_en-US.png)

3.  Click the **Values** tab. View or modify the corresponding deployment parameters.

    -   dockerImageRegistry: Enter the container image repository address in the annotations as the value according to the region in which the blockchain network is to be deployed \(inside or outside China\).
    -   externalAddress: Enter the EIP bound to the worker node to generate the connection profile.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2562_en-US.png)

4.  Click **DEPLOY**.
5.  Enter the cluster dashboard. Check the status of the pods related to the blockchain network. Wait until the statuses of all the pods become Running.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2563_en-US.png)


The Kubernetes service of the container dashboard also supports deploying the blockchain network by using Helm. For more information, see [Configure and deploy blockchain network](reseller.en-US/Block Chain/Configure and deploy blockchain network.md#).

## Step 6 Test blockchain network by using CLI {#section_h5k_ryt_vdb .section}

1.  On a master node of the Kubernetes cluster, run the following command to enter the CLI container.

    ```
    kubectl exec -it fabric-cli bash
    ```

2.  Run the following command to start the CLI test.

    ```
    ./cli-test.sh
    ```

3.  During the test, press any key to continue.
4.  If no errors occur during the test and the following words are displayed, it indicates that the test is successfully completed.

    ```
      ===================== Query on PEER4 on channel 'bankchannel' is successful ===================== 
      Press any key to continue...
      ===================== All GOOD, End-2-End execution completed =====================
    ```


## Step 7 Access blockchain explorer {#section_rcr_1zt_vdb .section}

1.  Run the command kubectl get svc on a master node of the Kubernetes cluster. You can also log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), enter the Kubernetes cluster dashboard, click **Services** in the left-side navigation pane, and check the external endpoint of the service <network name\>-explorer.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2564_en-US.png)

2.  Access the external endpoint in the browser.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/2565_en-US.png)


## Step 8 Delete blockchain network {#section_yc1_zqt_vdb .section}

1.  Under Kubernetes, click **Application** \> **Release** in the left-side navigation pane. Select the cluster from the Clusters drop-down list. Click **Delete** at the right of the release name of the blockchain network.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/6451_en-US.png)

2.  Click **OK** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/6106_en-US.png)


Then, the environment preparations, configuration and deployment, test, and deletion of the blockchain network are complete. For further development and test, you can repeat the steps of configuration, deployment, test, and deletion of the blockchain network. You can also customize the blockchain network environment according to actual requirements and product documents.

