# 通过阿里云日志服务采集 Kubernetes 集群日志 {#concept_vlm_3yz_vdb .concept}

日志服务支持通过 Logtail 采集 Kubernetes 集群日志，本文主要介绍 Logtail 的 DaemonSet 部署步骤。

## 配置流程 {#section_ldk_jyz_vdb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6942/4687_zh-CN.png)

[步骤一 部署 Kubernetes DaemonSet](#section_irb_q11_wdb)

[步骤二 配置机器组](#section_wyb_5gb_wdb)

在日志服务控制台创建自定义标识机器组，后续该 Kubernetes 集群伸缩无需额外运维。

[步骤三 创建采集配置](#section_d2k_jyz_vdb)

在日志服务控制台创建采集配置，所有采集均为服务端配置，无需本地配置。

## 步骤一 部署 Kubernetes DaemonSet {#section_irb_q11_wdb .section}

1.  连接到您的 Kubernetes 集群。

    参见[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

2.  配置参数。

    1.  单击下载 [日志服务 YAML 文件模板](http://logtail-release.oss-cn-hangzhou.aliyuncs.com/docker/k8s/logtail-daemonset.yaml)，用 vi 编辑器打开。
    2.  将 `env` 环境变量一节中所有的 `${your_xxxx}` 替换成您的真实值。

        |参数|参数说明|
        |`${your_region_name}`|地域名称。请替换为您创建的日志服务 project 所在的地域。地域名称请参考 [Logtail 安装所选 region 名](https://help.aliyun.com/document_detail/28982.html)。|
        |`${your_aliyun_user_id}`|用户标识，请替换为您的阿里云主账号用户 ID。主账号用户 ID 为字符串形式。有关如何查看 ID，参见 [用户标识配置](https://help.aliyun.com/document_detail/49007.html)[用户组标识配置](https://www.alibabacloud.com/help/zh/doc-detail/49007.htm)。|
        |`${your_machine_group_name}`|您集群的机器组标识，此部分由您自己命名，取值范围为 \[0-9a-zA-Z-\_\]，具体请参考 [自定义机器组](https://help.aliyun.com/document_detail/28983.html)。|

    **Note:** 

    -   您的主账号需要开启 AccessKey，参见[五分钟快速入门](../../cn.zh-CN/快速入门/五分钟快速入门.md#)。
    -   请您不要修改模板中的 `volumeMounts` 和 `volumes` 部分，否则会造成 Logtail 无法正常工作。
    -   您可以自定义配置 Logtail 容器的启动参数，只需保证如下几点即可：
        -   启动时，必须具备 3 个环境变量：`ALIYUN_LOGTAIL_USER_DEFINED_ID`、`ALIYUN_LOGTAIL_USER_ID`、`ALIYUN_LOGTAIL_CONFIG`。
        -   必须将 Docker 的 Domain Socket 挂载到/var/run/docker.sock。
        -   如果您需要采集其他容器或宿主机文件，需要将根目录挂载到 Logtail 容器的/logtail\_host目录。
3.  部署 Logtail 的 DaemonSet。

    示例：

    ```
    [root@iZu kubernetes]# curl http://logtail-release.oss-cn-hangzhou.aliyuncs.com/docker/k8s/logtail-daemonset.yaml > logtail-daemonset.yaml
    [root@iZu kubernetes]# vi logtail-daemonset.yaml
    ...
       env:
           - name: "ALIYUN_LOGTAIL_CONFIG"
             value: "/etc/ilogtail/conf/cn_hangzhou/ilogtail_config.json"
           - name: "ALIYUN_LOGTAIL_USER_ID"
             value: "16542189653****"
           - name: "ALIYUN_LOGTAIL_USER_DEFINED_ID"
             value: "k8s-logtail"
    ...
    [root@iZu kubernetes]# kubectl apply -f logtail-daemonset.yaml
    ```

    您可以通过 `kubectl get ds -n kube-system` 查看您的 Logtail agent 的运行状态。


## 步骤二 配置机器组 {#section_wyb_5gb_wdb .section}

1.  参见[五分钟快速入门](../../cn.zh-CN/快速入门/五分钟快速入门.md#)开通日志服务并创建 Project。
2.  在日志服务控制台的机器组列表页面单击 创**建机器组**。
3.  选择**用户自定义标识**，将您上一步配置的`ALIYUN_LOGTAIL_USER_DEFINED_ID`填入**用户自定义标识**内容框中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6942/4688_zh-CN.png)

    配置完成一分钟后，在机器组列表页面单击右侧的**查看状态**，即可看到已经部署 Logtail DaemonSet 节点的心跳状态。具体参见机器组配置中的**查看状态**。


## 步骤三 创建采集配置 {#section_d2k_jyz_vdb .section}

请根据您的需求在控制台创建 Logtail 采集配置，采集配置步骤请参考：

-   [容器文本文件（推荐）](../../cn.zh-CN/用户指南/Logtail 采集/数据源/容器文本日志.md#)
-   [容器标准输出 （推荐）](../../cn.zh-CN/用户指南/Logtail 采集/数据源/容器标准输出.md#)
-   [文本日志](../../cn.zh-CN/用户指南/Logtail 采集/数据源/文本日志.md#)

    默认宿主机根目录挂载到 Logtail 容器的/logtail\_host 目录，配置路径时，您需要加上此前缀。例如需要采集宿主机上/home/logs/app\_log/ 目录下的数据，配置页面中日志路径设置为/logtail\_host/home/logs/app\_log/。

-   [Syslog](../../cn.zh-CN/用户指南/Logtail 采集/数据源/Syslog.md#)
-   [自定义插件](../../cn.zh-CN/用户指南/Logtail 采集/数据源/自定义插件.md#)

## 其他操作 {#section_ayf_ygb_wdb .section}

**查看 Kubernetes 集群中 Logtail DaemonSet 状态**

您可以执行命令 `kubectl get ds -n kube-system` 查看 Logtail 运行状态。

**Note:** Logtail 默认的 namespace 为 `kube-system`。

**如何调整 Logtail 资源限制**

默认 Logtail 最多占用单核 40% CPU 和 200M 的内存，如需提升处理速度，需调整两部分参数：

-   yaml 模板中 `resources` 下的 `limits` 和 `requests`。
-   Logtail 启动配置文件，文件路径为 yaml 模板中的 `ALIYUN_LOGTAIL_CONFIG` 环境变量，修改方式参见[配置启动参数](../../cn.zh-CN/用户指南/Logtail 采集/安装/配置启动参数.md#)。

**强制更新 Logtail DaemonSet**

修改logtail-daemonset.yaml文件后，执行如下命令进行更新：

```
kubectl --namespace=kube-system  delete ds logtail
kubectl apply -f ./logtail-daemonset.yaml
```

**Note:** 强制更新期间可能会出现数据重复。

**查看 Logtail DaemonSet 的配置信息**

`kubectl describe ds logtail -n kube-system`

**查看 Logtail 的版本号信息、IP、启动时间等**

示例如下：

```
[root@iZbp1dsu6v77zfb40qfbiaZ ~]# kubectl get po -n kube-system -l k8s-app=logtail
NAME            READY     STATUS    RESTARTS   AGE
logtail-gb92k   1/1       Running   0          2h
logtail-wm7lw   1/1       Running   0          4d
[root@iZbp1dsu6v77zfb40qfbiaZ ~]# kubectl exec logtail-gb92k -n kube-system cat /usr/local/ilogtail/app_info.json
{
   "UUID" : "",
   "hostname" : "logtail-gb92k",
   "instance_id" : "0EBB2B0E-0A3B-11E8-B0CE-0A58AC140402_172.20.4.2_1517810940",
   "ip" : "172.20.4.2",
   "logtail_version" : "0.16.2",
   "os" : "Linux; 3.10.0-693.2.2.el7.x86_64; #1 SMP Tue Sep 12 22:26:13 UTC 2017; x86_64",
   "update_time" : "2018-02-05 06:09:01"
}
```

**查看 Logtail 的运行日志**

Logtail 运行日志保存在/usr/local/ilogtail/目录下，文件名为ilogtail.LOG，轮转文件会压缩存储为ilogtail.LOG.x.gz。

示例如下：

```
[root@iZbp1dsu6v77zfb40qfbiaZ ~]# kubectl exec logtail-gb92k -n kube-system tail /usr/local/ilogtail/ilogtail.LOG
[2018-02-05 06:09:02.168693]    [INFO]    [9]    [build/release64/sls/ilogtail/LogtailPlugin.cpp:104]    logtail plugin Resume:start
[2018-02-05 06:09:02.168807]    [INFO]    [9]    [build/release64/sls/ilogtail/LogtailPlugin.cpp:106]    logtail plugin Resume:success
[2018-02-05 06:09:02.168822]    [INFO]    [9]    [build/release64/sls/ilogtail/EventDispatcher.cpp:369]    start add existed check point events, size:0
[2018-02-05 06:09:02.168827]    [INFO]    [9]    [build/release64/sls/ilogtail/EventDispatcher.cpp:511]    add existed check point events, size:0    cache size:0    event size:0    success count:0
```

**重启某个 Pod 的 Logtail**

示例如下：

```
[root@iZbp1dsu6v77zfb40qfbiaZ ~]# kubectl exec logtail-gb92k -n kube-system /etc/init.d/ilogtaild stop
kill process Name: ilogtail pid: 7
kill process Name: ilogtail pid: 9
stop success
[root@iZbp1dsu6v77zfb40qfbiaZ ~]# kubectl exec logtail-gb92k -n kube-system /etc/init.d/ilogtaild start
ilogtail is running
```

