# 通过 SSH 访问 Jupyter 服务 {#concept_s4b_2mg_vdb .concept}

如果您在 [创建 Jupyter 环境](intl.zh-CN/深度学习解决方案/Swarm 集群/模型开发/创建 Jupyter 环境.md#) 环境时选择了 **启用SSH登录**，您可以通过本文档中介绍的方法 SSH 访问您的 Jupyter 服务。

本示例中，SSH 访问的端口映射为 192.\*\*\*.\*.\*\*.32775-\>22/tcp。其中，192.\*\*\*.\*.\*\* 是 ECS 实例的私网 IP，您无法通过此 IP 访问您的 Jupyter 服务。您需要通过 ECS 实例的弹性公网 IP 从外部 SSH 访问 Jupyter 服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/2047_zh-CN.png)

您可以登录到 [ECS 管理控制台](https://ecs.console.aliyun.com/?spm=a2c4g.11186623.2.4.Rw5M3O#/home) 查看该 ECS 实例绑定的弹性公网 IP。本示例中，弹性公网 IP 为 39.\*\*\*.\*\*.236。

**说明：** 如果 ECS 实例没有绑定弹性公网 IP，您需要先为其绑定弹性公网 IP，参见 [管理弹性公网 IP](https://www.alibabacloud.com/help/zh/doc-detail/51995.htm)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/2048_zh-CN.png)

此外，为了通过 ECS 实例的弹性公网 IP 从外部 SSH 访问您的 Jupyter 服务，您还需要配置安全组规则开放 32775 端口。

## 配置安全组规则 {#section_j23_rng_vdb .section}

1.  登录 [ECS 管理控制台](https://ecs.console.aliyun.com/?spm=a2c4g.11186623.2.6.Rw5M3O#/home)。
2.  单击左侧导航栏中的 **实例**，选择地域（本示例中为 **华南 1**），选择容器服务应用 mydevbox 对应的 ECS 实例。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/2049_zh-CN.png)

3.  单击右侧的 **更多** \> **安全组配置** 。
4.  选择容器服务集群对应的安全组并单击 **配置规则**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/4275_zh-CN.png)

5.  单击 **添加安全组规则**，填写规则信息并单击 **确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/2051_zh-CN.png)


## SSH 访问 Jupyter 服务 {#section_dwc_44g_vdb .section}

**Linux 环境下访问**

如果您使用的是 Linux 机器，你只需要运行以下命令即可 SSH 访问您的 Jupyter 服务。

```
ssh -p 32775 root@39.***.**.236
```

其中，`32775` 为 SSH 访问的端口；`39.***.**.236` 是您的 ECS 实例绑定的弹性公网 IP。

**Windows 环境下访问**

1.  运行 PuTTY，配置 Session。

    设置 IP 地址（ECS 实例的弹性公网 IP）、端口（SSH 访问的端口，本示例为 32775）和连接方式（**SSH**），并单击 **Open**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/3741_zh-CN.png)

2.  在弹出的会话窗口登录并访问服务。

    输入登录账号 root 并输入您在 [创建 Jupyter 环境](intl.zh-CN/深度学习解决方案/Swarm 集群/模型开发/创建 Jupyter 环境.md#) 时 **启用SSH登录** 所输入的 **SSH密码**。

    可以看到您成功访问到 Jupyter 服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7428/2053_zh-CN.png)


