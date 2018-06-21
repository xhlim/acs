# Kubernetes GPU集群支持GPU调度 {#concept_lmp_sq5_12b .concept}

自从1.8版本开始，Kubernetes已经明确表示要通过统一的[设备插件方式](https://kubernetes.io/docs/concepts/cluster-administration/device-plugins/)支持像Nvidia PU，InfiniBand,FPGA等硬件加速设备，而社区的GPU方案将在1.10全面弃用，并在1.11版本彻底从主干代码移除。

若您需要通过阿里云Kubernetes集群+GPU运行机器学习，图像处理等高运算密度等任务，无需安装nvidia driver和CUDA，就能实现一键部署和弹性扩缩容等功能。

## 使用须知 {#section_l3s_ss5_12b .section}

创建集群过程中，容器服务会进行如下操作：

-   创建 ECS，配置管理节点到其他节点的 SSH 的公钥登录，通过 CloudInit 安装配置 Kubernetes 集群。
-   创建安全组，该安全组允许 VPC 入方向全部 ICMP 端口的访问。
-   如果您不使用已有的 VPC 网络，会为您创建一个新的 VPC 及 VSwitch，同时为该 VSwitch 创建 SNAT。
-   创建 VPC 路由规则。
-   创建 NAT 网关及 EIP。
-   创建 RAM 子账号和 AK，该子账号拥有 ECS 的查询、实例创建和删除的权限，添加和删除云盘的权限，SLB 的全部权限，云监控的全部权限，VPC 的全部权限，日志服务的全部权限，NAS 的全部权限。Kubernetes 集群会根据用户部署的配置相应的动态创建 SLB，云盘，VPC路由规则。
-   创建内网 SLB，暴露 6443 端口。
-   创建公网 SLB，暴露 6443、8443和 22 端口（如果您在创建集群的时候选择开放公网 SSH 登录，则会暴露 22 端口；如果您选择不开放公网 SSH 访问，则不会暴露 22 端口）。

## 前提条件 {#section_ugh_hvv_ydb .section}

您需要开通容器服务、资源编排（ROS）服务和访问控制（RAM）服务。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

**说明：** 容器服务 Kubernetes 集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 集群前，您需要开通 ROS。

## 使用限制 {#section_kqj_pvv_ydb .section}

-   用户账户需有 100 元的余额并通过实名认证，否则无法创建按量付费的 ECS 实例和负载均衡。
-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络 VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 10 个节点。如果您需要创建更多的集群或者节点，请提交工单申请。
    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建 20 个EIP。
-   ECS 实例使用限制：
    -   仅支持 CentOS 操作系统。
    -   仅支持创建按量付费的 ECS 实例。

        **说明：** 实例创建后，您可以通过 ECS 管理控制台将[按量付费转预付费](../../../../cn.zh-CN/产品定价/按量付费转预付费.md#)。


## 创建GN5型Kubernetes集群 {#section_zmm_xs5_12b .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群**，进入集群列表页面。
3.  单击页面右上角的创建**Kubernetes 集群**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4292_zh-CN.png)

4.  填写集群的名称。

    集群名称应包含1-63个字符，可包含数字、汉字、英文字符或连字符（-）。

5.  选择集群所在的地域和可用区。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4293_zh-CN.png)

6.  设置集群的网络。Kubernetes 集群仅支持专有网络。

    **专有网络**：您可以选择**自动创建**（创建 Kubernetes 集群时，同步创建一个 VPC）或者**使用已有**（使用一个已有的 VPC）。选择**使用已有**后，您可以在已有 VPC 列表中选择所需的 VPC 和交换机。

    -   选择**自动创建**，创建集群时，系统会自动为您的 VPC 创建一个 NAT 网关。
    -   选择**使用已有**，如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关；如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

        **说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4294_zh-CN.png)

7.  设置 Master 节点的配置信息。

    您需要选择 Master 节点的系列和规格。

    **说明：** 

    -   目前仅支持 CentOS 操作系统。
    -   目前仅支持创建 3 个 Master 节点。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4299_zh-CN.png)

8.  设置 Worker 节点的配置信息。本例中将Worker节点作为GPU工作节点，选择GPU计算型gn5。
    1.  若您选择新增实例，则需要选择 Worker 节点的系列和规格，以及需要创建的 Worker 节点的数量（本示例创建2个GPU节点）。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14662/5929_zh-CN.png)

    2.  若您选择添加已有实例，则需要预先在此地域下创建GPU云服务器。
9.  设置是否为专有网络配置 SNAT 网关。。

    **说明：** 该选项仅在您选择 **使用已有** VPC 时才生效。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4302_zh-CN.png)

10. 设置是否开放公网 SSH 登录。

    -   选择开放公网 SSH 登录，您可以 SSH 访问集群。
    -   选择不开放公网 SSH 登录，将无法通过 SSH 访问集群，也无法通过 kubectl 连接 集群。如果您需要通过 SSH 访问集群实例，可以手动为 ECS 实例绑定 EIP，并配置安全组规则，开放 SSH（22）端口，具体操作参见[SSH访问Kubernetes集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#)。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14662/5928_zh-CN.png)

11. 设置是否启用云监控插件。

    您可以选择在 ECS 节点上安装云监控插件，从而在云监控控制台查看所创建 ECS 实例的监控信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4304_zh-CN.png)

12. 设置是否将节点 IP 添加到 RDS 实例的白名单。

    您可以选择将所创建节点的 IP 添加到 RDS 实例的白名单中，方便 ECS 实例访问 RDS 实例。

    **说明：** 您仅能将 ECS 实例的 IP 添加到位于同一地域同一 VPC 网络下的 RDS 实例的白名单中。

    1.  单击**请选择您想要添加白名单的RDS实例**。
    2.  在弹出的对话框中选择所需的 RDS 实例并单击**确定**。
13. 是否启用高级选项。
    1.  设置节点 Pod 数量，是指单个节点可运行 Pod 数量的上限，默认为 256，建议保持默认值。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4305_zh-CN.png)

    2.  设置是否选择**自定义镜像**。或不选择自定义镜像，则 ECS 实例会安装默认的 CentOS 版本。

        目前您只能选择基于 CentOS 的镜像来快速部署您需要的环境。

    3.  设置是否使用**自定义集群CA**。如果勾选自定义集群 CA，可以将 CA 证书添加到 kubernetes 集群中，加强服务端和客户端之间信息交互的安全性。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6880/4306_zh-CN.png)

14. 单击**创建集群**，启动部署。
15. 集群创建成功后，单击左侧导航栏中的**集群** \> **节点**，进入节点列表页面。
16. 选择所需的集群，选择创建集群时配置的Worker节点，单击右侧的**详情**，查看该节点挂载的GPU设备。

## 运行TensorFLow的GPU实验环境 {#section_xpj_bw5_12b .section}

数据科学家通常习惯使用Jupyter作为TensorFlow实验环境，我们这里可以用一个例子向您展示如何快速部署一个Jupyter应用。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **部署**，进入部署列表页面。
3.  单击页面右上角的创建**使用模板创建**。
4.  选择所需的集群，命名空间，选择样例模板或自定义，然后单击**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14662/5932_zh-CN.png)

    本例中，示例模板是一个Jupyter应用，包括一个deployment和service。

    ```
    ---
    # Define the tensorflow deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tf-notebook
      labels:
        app: tf-notebook
    spec:
      replicas: 1
      selector: # define how the deployment finds the pods it mangages
        matchLabels:
          app: tf-notebook
      template: # define the pods specifications
        metadata:
          labels:
            app: tf-notebook
        spec:
          containers:
          - name: tf-notebook
            image: tensorflow/tensorflow:1.4.1-gpu-py3
            resources:
              limits:
                nvidia.com/gpu: 1                      #指定调用nvidia gpu的数量
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                         # 指定访问Jupyter服务的密码，您可以按照您的需要修改
                value: mypassw0rd
    
    # Define the tensorflow service
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tf-notebook
    spec:
      ports:
      - port: 80
        targetPort: 8888
        name: jupyter
      selector:
        app: tf-notebook
      type: LoadBalancer                           #阿里云的负载均衡访问内部服务和负载均衡
    ```

    旧的GPU部署方案，您必须要定义如下的nvidia驱动所在的数据卷。

    ```
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    这需要您在编写部署文件时，强依赖于所在的集群，导致缺乏可移植性。但是在Kubernetes 1.9.3及之后的版本中，最终用户无需指定这些hostPath，nvidia的插件会自发现驱动所需的库链接和执行文件。

5.  单击左侧导航栏中的**应用** \> **服务**，选择所需的集群和命名空间，选择tf-notebook服务，查看外部端点。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14662/5933_zh-CN.png)

6.  在浏览器中访问Jupyter实例，访问地址是`http://EXTERNAL-IP`，输入模板中配置的密码。
7.  您可通过如下的程序，验证这个Jupyter实例可以使用GPU。它将列出Tensorflow可用的所有设备。

    ```
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14662/5934_zh-CN.png)


