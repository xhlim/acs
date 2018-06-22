# 容器服务 VPC 网络使用指南 {#concept_jdh_zht_xdb .concept}

本文档从容器服务使用 VPC 网络的角度来介绍如何在容器服务中正确地使用 VPC 网络以及相应的注意事项。

**VPC 网段**

-   为了可以顺利地在容器服务中创建 VPC 的容器集群，首先需要根据实际情况规划网络。创建 VPC 网络的时候，您必须指定对应的 CIDR 来划分对应的子网。
-   每个专有网络只能指定 1 个网段，网段范围如下，其中 172.16.0.0/12 是专有网络的默认网段。
    -   10.0.0.0/8
    -   172.16.0.0/12
    -   192.168.0.0/16

**容器网段**

在创建 VPC 网络的容器服务集群时，需要指定对应的容器网段，目前容器服务支持的容器网段如下：

-   192.168.1.0/24
-   172.\[16-31\].1.0/24

**网络规划**

为了保证容器间的网络互通，需要把每个容器网段都添加到路由表中；因此，为了避免网段冲突，需要提前根据 VPC 和容器网段对自身的应用服务进行相应的网络规划。

**网段规划**

在 VPC 网段和容器网段中，172 网段是重合的；因此，VPC 网段和容器网段选择 172 网段时，需要特别注意。下面举例来说明一下。

假设 VPC 网段选择为 172.16.0.0/12，交换机网段设定为 172.18.1.0/24。那么根据交换机的网段定义，归属于该交换机的 ECS 的 IP 地址应该是 172.18.1.1-172.18.1.252。如果此时，容器网段也定义为 172.18.1.0/24，那么就会出现容器的 IP 和 ECS 的 IP 重复的情况，在这种情况下会导致容器间的网络通信出现异常。因此，在使用 VPC 网络时，必须注意网络的规划。

**路由表规划**

目前，同一个 VPC 的最大路由表条目是 48 条。

## 示例 {#section_wrn_j3t_xdb .section}

下面以一个完整的示例流程，来演示整个容器服务 VPC 网络集群的创建流程。

**步骤 1 创建 VPC 网络**

1.  登录到 [VPC 管理控制台](https://vpc.console.aliyun.com/)。
2.  单击左侧导航栏中的**专有网络**。
3.  选择所需的地域（本示例中选择华东 1）并单击**创建专有网络**。
4.  填写专有网络的信息并单击**确定**。

    在本示例中，为了避免和容器网段可能的冲突，选择创建 10.0.0.0/8 网段的 VPC 网络。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5037_zh-CN.png)


**步骤 2 创建交换机**

VPC 创建好之后，在该 VPC 下创建相应的交换机。

1.  在专有网络列表中，选择刚创建的专有网络。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5038_zh-CN.png)

2.  单击右侧的**管理**。
3.  单击左侧导航栏中的**交换机**并单击右上角的**创建交换机**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5039_zh-CN.png)

4.  填写交换机的信息并单击**确定**。

    本示例中，创建华东 1 地域 10.1.1.0/24 网段的交换机。创建 ECS 时，如果选择该交换机，最终 ECS 的 IP 地址为 10.1.1.1-10.1.1.252，合计 252 个 IP 地址（也就是说该网段的交换机下可以购买 252 台 ECS）。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5040_zh-CN.png)


**步骤 3 查看路由表**

VPC 和交换机创建好之后，可以查看路由表的信息。

单击左侧导航栏中的**路由器**。可以看到，交换机的网络会被作为默认的系统路由表。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5041_zh-CN.png)

**步骤 4 创建容器集群**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**集群**。
3.  单击右上角的**创建集群**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5042_zh-CN.png)

4.  填写集群信息并单击**创建集群**。

    **地域**选择华东 1，**网络类型**选择专有网络，选择之前创建好的 VPC 和交换机。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5043_zh-CN.png)


本示例中，**容器起始网段**选择 172.18.0.0/24。那么，该集群内的节点上的容器网段为 172.18.\[1-254\].0/24，每个节点上容器的 IP 地址为 172.18.x.\[1-255\]。

**步骤 5 验证节点 IP**

容器集群创建成功后，可以通过校验集群节点 IP 地址，路由表，查看应用容器的 IP 地址等方式来验证之前提到的网络规划的理论。

通过查看容器集群的节点列表中 ECS 的 IP 地址，即可验证交换的网段。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**节点**。
3.  选择节点所在的集群。

    从下图可以看到新购的 ECS 的 IP 归属于 10.1.1.0/24 这个交换机网段。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5044_zh-CN.png)


**步骤 6 验证路由表**

验证完节点的 IP 地址，继续校验路由表。

1.  登录到 [VPC 管理控制台](https://vpc.console.aliyun.com/)。
2.  单击左侧导航栏中的**专有网络**。
3.  在专有网络列表中，选择刚创建的专有网络。
4.  单击右侧的**管理**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5038_zh-CN.png)

5.  单击左侧导航栏中的**路由器**。

    可以看到系统向该 VPC 的路由表里新增网段为 172.18.x.0/24 的路由信息，下一跳为对应的 ECS 实例的 ID。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5046_zh-CN.png)


**步骤 7 验证容器 IP**

最终，需要验证容器的 IP 是否正确。

本示例中，通过编排模板从容器服务管理控制台部署一个 WordPress 应用，然后通过查看某节点上的容器列表，来验证一下容器的 IP。

有关如何创建 WordPress 应用，参见[通过编排模板创建应用](intl.zh-CN/用户指南/Kubernetes 集群/应用管理/通过编排模板创建应用.md#)。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**应用**。
3.  选择应用所在的集群并单击应用的名称。
4.  单击**容器列表**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7074/5047_zh-CN.png)


以上的校验显示我们成功地创建了一个 VPC 网络的容器集群。

