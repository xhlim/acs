# Create a cluster {#concept_fqv_1v2_xdtdb .concept}

You can specify the configurations and the number of Elastic Compute Service \(ECS\) instances when creating clusters. You can also create a zero-node cluster, and then bind it with other ECS instances.

**Note:** The zero-node cluster is in the Inactive status after the creation and is activated with the Running status after you add ECS instances to it.  For how to add existing ECS instances to the cluster, see [Add an existing ECS instance](reseller.en-US/User Guide/Swarm cluster/Clusters/Add an existing ECS instance.md#).

## Instructions {#section_mxj_zv2_xdb .section}

Container Service performs the following operations when creating a cluster:

-   Create a Server Load Balancer instance with 80:9080 configured as the listener if the **Automatically Create Server Load Balancer** check box is selected.
-   Create a security group. The security group rules are as follows.

    **Virtual Private Cloud \(VPC\) inbound**

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4753_en-US.png)

-   Create a Resource Access Management \(RAM\) user if you have activated the RAM service.
-   Create the ECS instances and distribute the Internet IP address to the ECS instances if you select **Add** in the Add Node field. \(If the Network Type is VPC, distribute the Elastic IP \(EIP\) to the ECS instances and create the corresponding routing rules.\)
-   Use the configured **Logon Password** to configure the ECS instances.

    **Note:** Container Service does not save this password.

-   If the VPC node configuration fails, Container Service collects the standard output of the node creation and initialization. You can view the information in the cluster logs.

## Limits {#section_gck_ylt_xdb .section}

-   Server Load Balancer instances created with clusters are only available in Pay-As-You-Go mode.
-   By default, each account has a certain quota for the cloud resources they can create. The cluster fails to be created if the quota is exceeded.  Make sure you have enough quota before creating the cluster.  To increase your quota, open a ticket.
    -   By default, each account can create at most five clusters in all regions and add up to 20 nodes to each cluster.
    -   By default, each account can create at most 100 security groups.
    -   By default, each account can create at most 60 Pay-As-You-Go Server Load Balancer instances.
    -   By default, each account can create at most 20 EIPs.

## Procedure {#section_utc_zlt_xdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click Swarm \> **Clusters** in the left-side navigation pane. Click **Create Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4754_en-US.png)

3.  Complete the following configurations.

    -   **Cluster Name**: Enter the name of the cluster.  It can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens  \(-\).

        **Note:** The cluster name must be unique under the same account and the same region.

    -   **Region**: Select the region in which the cluster is to be deployed.
    -   **Zone**: Select the zone for the cluster.

        **Note:** You can select the region and zone according to the distribution of your servers.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4755_en-US.png)

4.  Select the network type of the cluster.  Currently, Container Service only supports VPC.

    Complete the corresponding configurations.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4756_en-US.png)

    VPC enables you to build an isolated network environment based on Alibaba Cloud. You can have full control over your own virtual network, including a free IP address range, Classless Inter-Domain Routing \(CIDR\) block division, and the configurations of route table and gateway.

    Specify a VPC, a VSwitchId, and the initial CIDR block of containers \(the subnet CIDR block to which the Docker containers belong. For ease of IP management, containers of different virtual machines belong to different CIDR blocks, and container subnet CIDR block cannot conflict with virtual machine CIDR block\).  We recommend that you build your own VPC/VSwitchId for the cluster to prevent issues  such as network conflicts.

5.  Select whether or not to add nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4757_en-US.png)

    You can create a cluster with several new ECS instances, or create a zero-node cluster and then add existing ECS instances to the cluster.  For how to add existing ECS instances to the cluster, see [Add an existing ECS instance](reseller.en-US/User Guide/Swarm cluster/Clusters/Add an existing ECS instance.md#).

    -   **Add**
        1.  Select the operating system for the node.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4758_en-US.png)

            Currently, Ubuntu 14.04/16.04 64bit and CentOS 7.4 64bit  are supported.

        2.  Configure the ECS instance specifications.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4759_en-US.png)

            You can select the generation, family, type, and quantity of the instance, disk type and capacity \(the ECS instance has a 20 GB  system disk by default\), and logon password.  Container Service uses the configured **Logon Password** to configure the ECS instances  when creating the cluster, but does not save this password.

            **Note:** 

            -   The data disk is mounted to the /var/lib/docker directory and used for the storage of Docker images and containers if you select the Attach Data Disk check box.
            -   In terms of performance and management, we recommend that you mount an independent data disk to the host  and manage the persistent data of containers by using Docker volumes.
    -   **Do not Add**

        You can click **Add Existing Instance** to add existing ECS instances to the cluster, or click Add Existing Instances on the Cluster List page to add existing ECS instances to the cluster after the cluster is created. For more information, see [Add an existing ECS instance](reseller.en-US/User Guide/Swarm cluster/Clusters/Add an existing ECS instance.md#).

6.  Select whether or not to configure public EIP.

    If you select VPC as the network type, Container Service configures an EIP for each ECS instance in the VPC environment  by default. If this is not required, select the **Do not Configure Public EIP** check box and then configure the SNAT gateway.

    **Note:** You can apply for up to 20 EIPs per account. To use VPC and create EIP automatically when creating a cluster, the cluster fails to be created if the number of EIPs under your account reaches its quota.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4760_en-US.png)

7.  Select whether or not to create a Server Load Balancer instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4761_en-US.png)

    The Automatically Create Server Load Balancer check box is selected by default. With this check box selected, a Server Load Balancer instance is created after the cluster is created.  You can access the container applications in the cluster by means of this Server Load Balancer instance. The created Server Load Balancer instance is in the Pay-As-You-Go mode.

8.  Select whether or not to install cloud monitoring plug-in on your ECS instances.

    To view the monitoring information of the created ECS instances in the CloudMonitor console, select the Install cloud monitoring plug-in on your ECS check box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4762_en-US.png)

9.  You can select to add the IP addresses of the ECS instances to the RDS instance whitelist.

    Adding the IP addresses of the ECS instances to the RDS instance whitelist facilitates the ECS instances to access the RDS instances.

    **Note:** 

    -   We recommend that you configure the RDS Whitelist when **Add** is selected for Add Node.
    -   If **Do not Add** is selected for Add Node and you want to configure the RDS Whitelist, add the existing ECS instances on the Create Cluster page. The RDS Whitelist cannot be configured if you create a zero-node cluster and add existing ECS instances after the cluster creation.
    -   The ECS instance must be in the same region as the RDS instance so that the IP address of the ECS instance can be added to the RDS instance whitelist.
    1.  Click **Select RDS Instances**. The Add to RDS instance whitelist dialog box appears.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4763_en-US.png)

    2.  Select the RDS instances and then click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4764_en-US.png)

10. Click **Create Cluster**.

    After the cluster is successfully created, you can configure the ECS instance or Server Load Balancer instance in the corresponding console.


## Subsequent operations {#section_jyj_zv2_xdb .section}

On the Cluster List page,  you can click **View Logs** at the right of the cluster to view the creation process logs of the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6990/4765_en-US.png)

You can create applications in the created cluster. For more information, see [Create an application](reseller.en-US/User Guide/Swarm cluster/Applications/Create an application.md#).

## References {#section_lyj_zv2_xdb .section}

If the cluster fails to be created, see[Common errors leading to cluster creation failure](../../../../reseller.en-US/FAQ/Swarm FAQs/Common errors leading to cluster creation failure.md#) for troubleshooting.

