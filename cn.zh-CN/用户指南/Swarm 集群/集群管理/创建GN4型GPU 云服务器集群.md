# 创建GN4型GPU 云服务器集群 {#CS_introduction_0006 .concept}

您可以创建集群，以使用 GN4 型 GPU 云服务器。

**说明：** 如果您选择创建一个零节点集群，创建完成后，集群会处于“待激活”状态，添加云服务器后就可以激活集群（变为“运行中”状态）。有关如何向集群中添加已有云服务器，参见 [添加已有节点](cn.zh-CN/用户指南/Swarm 集群/集群管理/添加已有节点.md#)。

## 前提条件 {#section_dkr_snk_xdb .section}

目前，按量付费的 GN4 型 GPU 云服务器以白名单方式开放给用户使用。如果您需要使用按量付费的 GN4 型 GPU 云服务器， 需要 [提交 ECS 工单](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail) 进行申请。

## 遇到没有资源的情况怎么办？ {#section_ekr_snk_xdb .section}

如果您已经通过了使用 GN4 型 GPU 云服务器的申请，但是在选择**实例规格** 时，未找到 GN4 型 GPU 云服务器（32核 48GB（ecs.gn4.8xlarge）或 56核 96GB（ecs.gn4.14xlarge）），建议您采取以下措施：

-   更换地域
-   更换可用区

如果依然没有资源，建议您耐心等待一段时间再购买。实例资源是动态的，如果资源不足，阿里云会尽快补充资源，但是需要一定时间。您可以在晚些时候或者次日再尝试购买。

## 使用限制 {#section_zzc_5nk_xdb .section}

-   目前，容器服务仅支持在华南 1 、华东 2 和华北 2 地域创建 GN4 型 GPU 云服务器集群。
-   目前，GN4 型 GPU 云服务器只支持专有网络（VPC）。
-   为了保证您的 GN4 型 GPU 云服务器集群的使用效果，建议集群中统一使用 GN4 型 GPU 云服务器，不要添加其它规格族的 ECS 实例到 GN4 型 GPU 云服务器集群。
-   默认情况下，您最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 20 个节点。如果您需要创建更多的集群或添加更多的节点，请提交 [工单申请](https://selfservice.console.aliyun.com/ticket/scene?productCode=cs&productName=%E5%AE%B9%E5%99%A8%E6%9C%8D%E5%8A%A1)。
-   目前负载均衡只支持按量付费的方式，后续将提供更多选择。
-   用户账户需有 100 元的余额并通过实名认证，否则无法创建按量付费的 ECS 实例和负载均衡。

## 操作步骤 {#section_awv_b4k_xdb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/#/overview/all)。
2.  单击左侧导航中的**集群**，单击右上角的**创建集群**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4766_zh-CN.png)

3.  设置集群的基本信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4767_zh-CN.png)

    -   **集群名称：** 要创建的集群的名称。可以包含 1~63 个字符，包括数字、中文字符，英文字符和连字符 （-）。

        **说明：** 集群名称在同一个用户和同一个地域下必须唯一。

    -   **地域：** 所创建集群将要部署到的地域。选择**华南 1**、**华东 2**或、****。

        **说明：** 目前，仅支持在华南 1、华东 2 和华北 2地域创建 GN4 型 GPU 云服务器集群。

    -   **可用区：**集群的可用区。

        **说明：** 您可以根据您的服务器分布情况，选择不同的地域和可用区。

4.  设置集群的网络类型为**专有网络**并配置相关信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4768_zh-CN.png)

    专有网络 VPC 支持您基于阿里云构建一个隔离的网络环境，您可以完全掌控自己的虚拟网络，包括自由 IP 地址范围、划分网段、配置路由表和网关等。

    专有网络需要您指定一个 VPC、一个 VSwitchId 和容器的起始网段（Docker 容器所属的子网网段，为了便于 IP 管理，每个虚拟机的容器属于不同网段，容器子网网段不能和虚拟机网段冲突）。

    为了防止网络冲突等问题，建议您为容器集群建立属于自己的 VPC/VSwitchId。

5.  添加节点。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4769_zh-CN.png)

    您可以在创建集群的同时创建若干个节点，或者创建一个零节点集群并添加已有云服务器。有关如何添加已有云服务器的详细信息，参见[添加已有节点](cn.zh-CN/用户指南/Swarm 集群/集群管理/添加已有节点.md#)。

    -   **创建节点**
        1.  设置节点的操作系统。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4770_zh-CN.png)

            目前支持的操作系统包括 Ubuntu 16.04 64 位和 CentOS 7.4 64 位。

        2.  设置云服务器的实例规格。

            -   实例系列选择**系列III**。
            -   实例规格选择32核 48GB（ecs.gn4.8xlarge） 或 56核 96GB（ecs.gn4.14xlarge）。

                **说明：** 如果您已经通过了 GN4 型 GPU 云服务器的使用申请，但是未找到这两种实例规格，说明目前这两种规格的实例没有资源，建议晚些时候或者次日再尝试购买。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4770_zh-CN.png)

            您可选择实例的数量，并指定数据盘的容量（云服务器默认带有 20G 大小的系统盘）和登录密码。

            **说明：** 

            -   如果您选择了数据盘，它会被挂载到/var/lib/docker目录，用于 Docker 镜像和容器的存储。
            -   从性能和管理考虑，建议您在宿主机挂载独立的数据盘，并利用 Docker 的 volume 对容器的持久化数据进行管理。
    -   **添加已有节点**

        您可以单击下边的**选择已有实例**将已有的云服务器添加到集群中，或者直接单击**创建集群**等集群创建完成后再通过集群列表页面添加已有云服务器。

        **说明：** 为了保证您的 GN4 型 GPU 云服务器集群的使用效果，建议集群中统一使用 GN4 型 GPU 云服务器，不要添加其它规格族的 ECS 实例到 GN4 型 GPU 云服务器集群。

6.  配置 EIP。

    当您将网络类型设置为 VPC 时，容器服务会默认给每一个专有网络下的云服务器配置一个EIP。如果不需要，您可以勾选**不配置公网EIP** 复选框，但是需要额外配置 SNAT 网关。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4772_zh-CN.png)

7.  创建一个负载均衡实例。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4773_zh-CN.png)

    目前创建集群会默认创建一个负载均衡实例。您可以通过这个负载均衡实例访问集群内的容器应用。所创建的负载均衡实例为按量付费实例。

8.  在 ECS 节点上安装云监控插件。

    您可以选择在节点上安装云监控插件，从而在云监控控制台查看所创建 ECS 实例的监控信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4774_zh-CN.png)

9.  单击**创建集群**。

    集群创建成功后，您可以在集群列表页面单击所创建集群的名称查看集群中节点的信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4775_zh-CN.png)

    如果需要单独配置云服务器或负载均衡，可以去相应的控制台进行相关操作。


## 后续操作 {#section_zkr_snk_xdb .section}

您可以查看集群创建日志。在集群列表页面，选择所创建的集群并单击**查看日志**。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6992/4776_zh-CN.png)

您可以在创建的集群中创建应用。有关创建应用的详细信息，参见[创建应用](cn.zh-CN/用户指南/Swarm 集群/应用管理/创建应用.md#)。

