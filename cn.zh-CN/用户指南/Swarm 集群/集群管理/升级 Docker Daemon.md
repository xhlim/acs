# 升级 Docker Daemon {#task_exd_mwl_xdbafaf .task}

集群内的每一台服务器都会安装标准的 Docker Daemon 用于管理容器。

**Note:** 

-   集群升级需要机器可以公网访问，以便下载升级所需的软件包。
-   集群升级 Docker 过程中，可能会有升级失败的情况，为了您的数据安全，强烈推荐您选择先打快照然后再升级的方式。
-   集群升级 Docker 过程中，集群上部署的服务会中断，同时无法进行集群和应用的操作，请您在升级之前安排好相关事宜。升级时间大约3~30分钟， 升级完成后集群会变成运行中状态。

您可以在集群列表页面查看集群的 Docker 版本，如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4837_zh-CN.png)

1.   登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.   在 Swarm 菜单下，单击左侧导航栏中的**集群**。 
3.   选择您要升级 Docker Daemon 的集群，单击 Docker 版本列下的**升级** 或者单击**更多** \> **升级Docker**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4840_zh-CN.png)

4.   如果目前系统的 Agent 不是最新版本，需要先升级 Agent。单击**升级Agent**，根据提示进行相应的操作即可，如下图所示。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4841_zh-CN.png)

5.   若 Agent 已是最新版本，则可以直接升级 Docker。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7005/4842_zh-CN.png)

    您可以使用以下方法之一升级 Docker：

    -   直接升级

        单击**直接升级**，进入升级 Docker Engine 的流程。

    -   备份快照并升级

        建议您通过备份快照升级 Docker（以便于在升级过程中出问题后，可以通过快照进行恢复）。

        单击**备份快照并升级**，此时系统会调用 ECS OpenAPI 对集群内的节点打快照。

        由于备份快照需要一点时间，您需要耐心等待一会。完成快照备份后，系统自动进入升级 Docker Engine 的流程。

        如果备份快照失败，**继续升级** 和 **放弃升级**可用。您可以单击**继续升级**进入升级 Docker Engine 的流程，或者单击**放弃升级**放弃升级 Docker Engine。


此时，返回集群列表页面。您可以看到刚才操作的集群处于`Docker-Engine升级中`的状态。由于升级 Docker Engine 会进行相应的容器数据备份等工作，所以比较耗时，请耐心等待一会。

