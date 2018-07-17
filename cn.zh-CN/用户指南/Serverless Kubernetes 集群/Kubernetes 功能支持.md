# Kubernetes 功能支持 {#concept_pkm_jzz_xdb .concept}

**API 版本**

支持 Kubernetes 1.9 API。

**应用负载**

-   支持Deployment、Job/CronJob、Bare Pod。
-   不支持 DaemonSet。

**Pod 定义**

-   支持启动多个容器，设置环境变量，设置 RestartPolicy，设置健康检查命令，挂载volumes 等。
-   暂不支持 ServiceAccount、Affinity。

**负载均衡**

-   支持创建 LoadBalancer 类型应用。
-   支持Ingress。
-   不支持 NodePort 类型。

**配置**

支持 Secret 和 Configmap。

**存储**

-   支持 emptyDir 和 nfs volume 类型。
-   不支持 PersistentVolume 和 PersistentVolumeClaim。

命名空间

用户只可看见 default 命名空间，不可添加命名空间。

**节点**

用户不可查看 kubernetes 的节点信息。

**事件**

用户可查看 default 命名空间下的事件。

**容器logs**

用户可通过kubectl logs实时查看容器的日志。

**容器exec/attach**

用户可通过kubectl exec进入容器执行命令。

