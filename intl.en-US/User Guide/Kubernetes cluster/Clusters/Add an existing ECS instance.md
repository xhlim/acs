# Add an existing ECS instance {#task_rtr_h3g_vdb .task}

You can add existing Elastic Compute Service \(ECS\) instances to a created Kubernetes cluster. Currently, Kubernetes clusters only support adding worker nodes.

-   If you have not created a cluster before, create a cluster first. For how to create a cluster, see [Create a cluster](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   Add the ECS instance to the security group of the Kubernetes cluster first.

**Instructions**

-   By default, each cluster can contain at most 20 worker nodes. To add more nodes, open a ticket.
-   The ECS instance to be added must be in the same region and Virtual Private Cloud \(VPC\) as the cluster.
-   When adding an existing ECS instance, make sure that your ECS instance has an Elastic IP \(EIP\) for the network type VPC, or the corresponding VPC has configured the NAT gateway. In short, make sure the corresponding node can access the Internet normally. Otherwise, the ECS instance fails to be added.
-   The ECS instance to be added must be under the same account as the cluster.
-   Only the ECS instance whose operating system is CentOS can be added.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs%20%22Container%20Service%20console%22). 
2.   Click Kubernetes \> **Clusters** in the left-side navigation pane. 
3.   Click More at the right of the cluster**that you want to add ECS instances** \> **and then select Add Existing Instance from the drop-down list**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4319_en-US.png)

4.   The Add Existing ECS Instance page appears. All the available ECS instances under the current account are displayed on this page. Select to add existing ECS instances automatically or manually. 

    If Automatically Add is selected, select the ECS instances to add them to this cluster automatically. If Manually Add is selected, you must obtain the command and then log on to the corresponding ECS instance to add the ECS instance to this cluster. You can only add one ECS instance at a time.

5.   Select Automatically Add to add multiple ECS instances at a time. 
    1.   Select the ECS instances to be added and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4320_en-US.png)

    2.   Configure the logon password and then click **Next Step**. 

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/64983/cn_zh/1524193684199/Image%2010.png)

    3.   Click **Confirm** in the displayed dialog box. The selected ECS instances are automatically added to this cluster. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4322_en-US.png)

6.   You can also select Manually Add to manually add an existing ECS instance to this cluster. 
    1.   Select the ECS instance to be added and then click **Next Step**. You can only add one ECS instance at a time. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4323_en-US.png)

    2.   Confirm the information and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4324_en-US.png)

    3.   Copy the command. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4325_en-US.png)

    4.   Log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs). Select the region in which the cluster resides. 
    5.   Click **Connect** at the right of the ECS instance to be added. The Enter VNC Password dialog box appears. Enter the VNC password and then click **OK**. Enter the copied command and then click **OK** to run the script. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/4326_en-US.png)

    6.   After the script is successfully run, the ECS instance is added to the cluster. You can click the cluster ID on the Cluster List page to view the node list of the cluster and check if the ECS instance is successfully added to the cluster. 

