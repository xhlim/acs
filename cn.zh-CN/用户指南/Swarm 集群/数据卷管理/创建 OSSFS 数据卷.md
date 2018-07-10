# 创建 OSSFS 数据卷 {#concept_nsx_5lr_xdb .concept}

OSSFS 是阿里云官方提供的基于 FUSE 的文件系统（项目主页见[https://github.com/aliyun/ossfs](https://github.com/aliyun/ossfs)）。OSSFS 数据卷可以将 OSS 的 Bucket 包装成数据卷。

由于数据需要经过网络同步到云端，OSSFS 在性能和功能上与本地文件系统有差距。请不要把数据库等重 IO 应用、日志等需要不断改写文件的应用运行在 OSSFS 上。OSSFS 比较适合多容器之间共享配置文件，或者附件上传等没有改写操作的场景。

**OSSFS 和本地文件系统具体差异如下所示：**

-   随机或者追加写文件会导致整个文件的重写。
-   因为需要远程访问 OSS 服务器，元数据操作（例如 list directory）性能较差。
-   文件/文件夹的重命名操作不是原子的。
-   多个客户端挂载同一个 OSS Bucket 时，需要您自行协调各个客户端的行为。例如，避免多个客户端写同一个文件等等。
-   不支持硬链接（hard link）。

## 前提条件 {#section_a5g_yjt_xdb .section}

您的集群必须满足以下两个条件，才可以开通数据卷功能：

-   集群 Agent 的版本为 0.6 或更高。

    您可以在集群列表页面查看您的 Agent 的版本。选择所需的集群，单击右侧的**更多** \> **升级Agent**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5050_zh-CN.png)

    如果您的 Agent 版本低于 0.6，请先升级您的 Agent。有关如何升级 Agent，参见[升级 Agent](intl.zh-CN/用户指南/Swarm 集群/集群管理/升级 Agent.md#)。

-   集群里部署了 acsvolumedriver 应用。建议您将 acsvolumedriver 升级到最新版本。

    您可以通过升级系统服务部署和升级 acsvolumedriver 应用。详细操作参见[升级系统服务](intl.zh-CN/用户指南/Swarm 集群/集群管理/升级系统服务.md#)。

    **说明：** 升级或重启 acsvolumedriver 时，使用 OSSFS 数据卷的容器会重启，您的服务也会重启。


## 步骤 1 创建 OSS bucket {#section_d5g_yjt_xdb .section}

1.  登录 [对象存储管理控制台](https://oss.console.aliyun.com/)
2.  创建一个 bucket。

    本示例创建了一个位于华东 1 地域的 bucket。


## 步骤 2 创建 OSSFS 数据卷 {#section_f5g_yjt_xdb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Swarm 菜单下，单击左侧导航栏中的**数据卷**。
3.  选择需要创建数据卷的集群并单击页面右上角的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5052_zh-CN.png)

4.  在弹出的对话框中，选择**数据卷类型**为**OSS**，设置数据卷参数并单击**创建**。容器服务会在集群的所有节点上创建名称相同的数据卷。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7077/5053_zh-CN.png)

    -   **数据卷名**：数据卷的名称。数据卷名在集群内必须唯一。
    -   **AccessKey ID**、**AccessKey Secret**：访问 OSS 所需的 AccessKey。您可以从 [阿里云账号 AccessKey 控制台](https://ak-console.aliyun.com/) 获取。
    -   **Bucket ID**：您要使用的 OSS bucket 的名称。单击**选择Bucket**，在弹出的对话框中选择所需的 bucket 并单击**选择**。
    -   **访问域名**：如果 Bucket 和 ECS 实例位于不同地域（Region），请选择**外网域名**；如果位于相同地域，需要根据集群网络类型进行选择，若是 VPC 网络，请选择**VPC域名**，若是经典网络，请选择**内网域名**。
    -   **文件缓存**：如果需要在不同机器间同步同一个文件的修改（比如在机器 A 中修改文件，在机器 B 中读取修改后的内容），请关闭文件缓存。

        **说明：** 关闭文件缓存将导致 ls文件夹变得很缓慢，尤其是同一个文件夹下文件比较多时。因此，没有上述需求时，请打开文件缓存，提高ls命令的速度。


您可以在数据卷列表页面查看创建的 OSSFS 数据卷。

## 后续操作 {#section_m5g_yjt_xdb .section}

创建数据卷之后，您可以在您的应用中使用创建的数据卷。有关如何在应用中使用数据卷，参见[使用第三方数据卷](intl.zh-CN/用户指南/Swarm 集群/数据卷管理/使用第三方数据卷.md#)。

