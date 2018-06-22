# 使用 TensorFlow Serving {#concept_nmm_tvm_vdb .concept}

利用阿里云弹性计算资源（ECS 或 EGS），负载均衡和对象存储服务，对于 TensorFlow 的导出模型，快速部署服务上线，用于其他应用的访问。

## 准备工作 {#section_mxk_vvm_vdb .section}

在运行模型训练任务之前，请确认以下工作已经完成：

-   创建包含适当数量弹性计算资源（ECS 或 EGS）的容器集群。参见 [创建容器集群](intl.zh-CN/深度学习解决方案/Swarm 集群/环境准备/创建容器集群.md#)。
-   使用相同账号创建对象存储服务（OSS）Bucket，使用它保存用于模型训练的数据。
-   为上面的容器集群创建数据卷，用于将 OSS Bucket 作为本地目录挂载到执行训练任务的容器内。创建方法请参考 [创建数据卷](intl.zh-CN/深度学习解决方案/Swarm 集群/环境准备/创建数据卷.md#)。
-   了解 TensorFlow Serving 的基本概念和工作流程。参见 [Serving a TensorFlow Model](https://tensorflow.github.io/serving/serving_basic)。

## 约定 {#section_oxk_vvm_vdb .section}

为了简化您对于模型预测的使用，请注意以下约定。

-   在 OSS 的根目录以模型名创建文件夹。
-   在模型预测中，负载均衡的前、后端端口和应用端口必须保持一致。

## 操作步骤 {#section_inb_nwm_vdb .section}

1.  将预测模型保存在共享存储中。

    可以通过在 OSS 客户端上传模型文件夹完成。

    1.  在 OSS Bucket 的根目录以模型名创建文件夹，在本例子中使用 mnist。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2286_zh-CN.png)

    2.  将带版本号的 Tensorflow Serving 模型文件夹上传到 mnist 下。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2288_zh-CN.png)

2.  配置负载均衡的监听端口。
    1.  在 [负载均衡管理控制台](https://slbnew.console.aliyun.com/?spm=5176.2020520001.1001.113.T73V2a) （单击页面右上角的 **创建负载均衡**）创建一个用于路由的负载均衡实例。

        本示例中选择的是公网实例，您可以根据自己的需要选择公网或者私网。

        **说明：** 由于负载均衡不支持跨地域（Region）部署，因此应选择与您所使用容器服务集群相同的地域。

    2.  返回 [负载均衡管理控制台](https://slbnew.console.aliyun.com/?spm=5176.2020520001.1001.113.T73V2a)，将购买创建的负载均衡实例命名为 TensorFlow-serving。容器服务会通过该名称来引用这个负载均衡实例。

        单击左侧导航栏中的 **实例管理** \> 选择实例所在的地域 \> 选择所需实例 \> 编辑实例的名称并单击 **确定**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2289_zh-CN.png)

    3.  创建监听端口。

        单击实例右侧的 **管理** \> 单击左侧导航栏中的 **监听** \> 单击 **添加监听** \> 设置监听配置。协议为 TCP，端口映射为 8000:8000。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2291_zh-CN.png)

3.  启动模型预测。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/#/overview/all)。
    2.  单击左侧导航栏中的 **镜像与方案** \> **解决方案**。
    3.  在 **模型预测** 框中单击 **创建**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2293_zh-CN.png)

    4.  设置模型预测任务的基本信息。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2295_zh-CN.png)

        -   **集群：**选择将要运行模型预测的集群。
        -   **应用名：**为运行单机模型预测的应用名称。名称可以包含 1~64 个字符，包括数字、英文字母和连字符（-）。
        -   **训练框架：**选择用来进行模型训练的框架，包括 TensorFlow Serving 和自定义镜像。这里选择 TensorFlow Serving。
        -   **模型名称：**这里的模型名称要与步骤 1 中创建的模型文件夹一致。
        -   **实例数量：**TensorFlow Serving 实例的数量，这里不能超过集群中节点数。
        -   **GPU数量：**指定所使用的 GPU 数量，如果为 0 表示不使用 GPU（将使用 CPU 预测）。
        -   **数据卷名：**指定为用于存储预测模型的对象存储服务（OSS）Bucket 在该集群中创建的数据卷的名称。数据卷的创建方法，请参考 [创建数据卷](intl.zh-CN/深度学习解决方案/Swarm 集群/环境准备/创建数据卷.md#)。
        -   **负载均衡实例：**请选择步骤 2 负载均衡实例名称。
        -   **负载均衡端口：**请将步骤 2 设置的端口号填入。
4.  设置完毕后，单击 **确定**。
5.  在应用列表页面中，单击上述创建的应用。
6.  单击 **路由列表**，可以看到负载均衡提供的终端地址。这样您就可以利用 grpc 的客户端访问负载均衡地址:负载均衡端口，在本例子中为 101.37.193.36:8000。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7434/2296_zh-CN.png)


