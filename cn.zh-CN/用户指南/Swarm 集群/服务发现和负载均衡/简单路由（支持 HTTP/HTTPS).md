# 简单路由（支持 HTTP/HTTPS\) {#concept_scm_cvs_xdb .concept}

## 适用场景 {#section_e5f_25y_xdb .section}

普通且简单的 7 层协议负载均衡，Web 路由服务，容器集群内服务之间 7 层协议互相访问的通信代理和负载均衡。

## 原理 {#section_f5f_25y_xdb .section}

如下图所示，当您新建一个集群的时候，会默认给这个集群分配一个负载均衡实例。该负载均衡实例会将集群中的所有节点加入作为后端，同时前端会暴露 80 端口，后端所有节点的机器会暴露 9080 端口。容器服务会启动一个路由应用 `acsrouting`，即阿里云容器服务路由应用（Alibaba Cloud Container Service Routing）。该路由应用只有一个服务，即路由服务。该服务是全局（global）的，即每个节点（下面说到的主机和节点都是同一个意思，即 ECS 的 vm 实例）都部署了该服务（或者说镜像）的一个拷贝，也就是容器。每个节点都由这个容器用来路由 HTTP 服务或者 HTTPS 服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5531_zh-CN.png)

如上图所示，HTTP 服务，负载均衡实例的前后端端口的映射为 `80:9080`，主机与路由容器之间的端口映射为 `9080:80`，即路由的容器暴露 80 端口，其它用作 Web 服务的容器可以暴露任意的端口。只要在容器启动的时候设置主机和容器端口的映射，routing 服务就能获取到相应的端口进行请求的路由。有关暴露 HTTP 服务的完整示例，参见[通过镜像创建 Nginx](../../../../intl.zh-CN/快速入门/Swarm 集群/通过镜像创建 Nginx.md#)。

## 设置方式 {#section_cwm_35y_xdb .section}

**说明：** 请务必确保容器服务相关节点的内核参数（`/etc/sysctl.conf`*）中，如下配置值为 0，否则可能会导致无法访问：*

```
net.ipv4.conf.default.rp_filter = 0
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.eth0.rp_filter = 0
```

## 通过容器服务管理控制台进行设置 {#section_i5f_25y_xdb .section}

**通过 服务 \> 变更配置 进行设置**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**服务**。
3.  选择所要暴露的服务所在的集群。
4.  选择所要暴露的服务（本示例中为 wordpress-web）并单击**变更配置**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5533_zh-CN.png)

5.  在变更配置页面，配置主机和容器端口的映射，如下图所示。

    主机端口为空，表示随机暴露一个主机的端口（暴露 HTTP/HTTPS 服务时，您可以不需要知道主机暴露的具体端口是什么，可以使用 overlay 网络或者 VPC 网络来直接访问容器的端口），容器端口为 80。您使用 wordpress-web 服务的 80 端口来提供 HTTP 服务，使用的协议是 TCP 协议。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5534_zh-CN.png)

    路由配置通过域名来暴露服务，须标明要暴露的端口，此处为 Web 服务 80 端口。域名字段只填写了域名前缀，如果域名前缀为 `XXX`，获得的域名为 `XXX.$cluster_id.$region_id.alicontainer.com` 供测试使用。此处您获得的域名为 `wordpress.cb668bde43f054cd7bd515c8739f38310.cn-hangzhou.alicontainer.com`。您也可以填写自己的域名，需要添加解析到相应的负载均衡实例 IP。关于配置路由的容器端口和 HTTP 服务的域名的详细信息，参见[routing](intl.zh-CN/用户指南/Swarm 集群/服务编排/routing.md#)。

6.  配置完毕后，单击**更新**，然后返回服务列表，进入服务详情页，单击**访问端点**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5535_zh-CN.png)

7.  您可以成功访问 wordpress 欢迎页。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5536_zh-CN.png)


**通过应用的模板编辑器进行设置**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**应用**。
3.  选择目标应用所在的集群。
4.  选择目标应用（本示例中为 wordpress）并单击**变更配置**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5537_zh-CN.png)

5.  在模板编辑器中，添加[routing](intl.zh-CN/用户指南/Swarm 集群/服务编排/routing.md#) 标签，定义相应的域名或者域名前缀。注意升级应用的版本，以及确定是否拉取最新的 Docker 镜像，最后单击**确定**更新域名，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5538_zh-CN.png)

6.  您也可以在路由列表下访问**路由地址**，进入 wordpress 应用欢迎页。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7104/5539_zh-CN.png)


## 通过客户端工具进行设置 {#section_usr_fsx_zdb .section}

-   docker help run：查看使用的 [“-p” 选项](https://docs.docker.com/engine/reference/run/#expose-incoming-ports)， 路由配置在容器服务管理控制台进行。
-   docker-compose：查看支持的 [“ports” 选项](https://docs.docker.com/compose/compose-file/#ports)，路由配置规则详情见 [routing](intl.zh-CN/用户指南/Swarm 集群/服务编排/routing.md#)。

