# 通过 Web 界面创建路由 {#concept_qm4_kyn_vdb .concept}

阿里云容器服务 Web 界面集成了路由（Ingress）服务，您可通过 Web 界面快速创建路由服务，构建灵活可靠的流量接入层。

## 前提条件 {#section_kj5_nyn_vdb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)，并且集群中 Ingress controller 正常运行。
-   SSH 登录到 Master 节点，参见[SSH 访问 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#)。

## 步骤1 创建 deployment 和服务 {#section_c1p_ryn_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，进入路由页面。
3.  单击页面右上角**使用模板创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4535_zh-CN.png)

4.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    本例中，示例模板是一个 nginx 的 deployment。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4536_zh-CN.png)

5.  单击左侧导航栏中的**应用** \> **服务** ，进入服务列表页面，单击右上角**创建**。

    本例中，为了测试复杂路由服务，您需要创建相应的多个服务，并将服务绑定到后端 deployment 上，采用 clusterIP 的服务类型。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4537_zh-CN.png)

    创建完成后，在服务列表，您可看到本示例创建的 3 个服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4538_zh-CN.png)


## 步骤2 创建路由 {#section_gwv_tyn_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **路由**，进入路由页面。
3.  选择所需的集群和命名空间，单击页面右上角的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4539_zh-CN.png)

4.  在弹出的路由创建对话框中，首先配置路由名称，本例为 nginx-ingress。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4542_zh-CN.png)

5.  对路由规则进行配置。

    路由规则是指授权入站到达集群服务的规则，通常是 http 规则，配置项包括域名\(虚拟主机名称\)、URL 路径、服务名称和端口配置等。详细的信息，请参见[路由配置说明](intl.zh-CN/用户指南/Kubernetes 集群/路由管理/路由配置说明.md#)。

    本例中配置添加一条复杂的路由规则，配置集群默认的测试域名和虚拟主机名称，展示基于域名的路由服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4543_zh-CN.png)

    -   基于默认域名的简单路由，即使用集群的默认域名对外提供访问服务。
        -   **域名配置**：使用集群的默认域名，本例中是 `test.[cluster-id].[region-id].alicontainer.com`。

            在创建路由对话框中，会显示该集群的默认域名，域名格式是 `*.[cluster-id].[region-id].alicontainer.com`；您也可在集群的基本信息页面中获取。

        -   **服务配置**：配置服务的访问路径、名称以及端口。
            -   访问路径配置：指定服务访问的 URL 路径，如/svc1，每个路径 （path）都关联一个 backend （服务），在阿里云 SLB 将流量转发到 backend 之前，所有的入站请求都要先匹配域名和路径。
            -   服务名称及端口配置：即 backend 配置，是一个 `service:port` 的组合，比如 `http-svc1:80`。Ingress的流量被转发到它所匹配的 backend。
    -   基于域名的简单扇出路由。本例中使用一个虚拟的主机名称作为测试域名对外提供访问服务。若您在生产环境中，可使用成功备案的域名提供访问服务。
        -   **域名配置**：本例中使用测试域名 `foo.bar.com`。

            您需要修改 hosts 文件添加一条域名映射规则。

            ```
            118.178.108.143 foo.bar.com
            ```

        -   **服务配置**：配置服务的访问路径、服务名称以及端口。
            -   访问路径配置：指定服务访问的 URL 路径，如/svc2 和 /svc3。
            -   服务名称及端口配置：本例中指定 `http-svc2:80` 和 `http-svc2:80` 。
6.  配置注解。

    单击**重定向注解**，可为路由添加一条典型的重定向注解。即 `nginx.ingress.kubernetes.io/rewrite-target：/`，表示将/path路径重定向到后端服务能够识别的/路径上面。

    您也可单击**添加**按钮，输入注解名称和值，即 ingress 的 annotation 键值对，如常见的重写注解，参见 [rewrite annotation](https://github.com/kubernetes/ingress-nginx/tree/master/docs/examples/rewrite)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4544_zh-CN.png)

7.  配置 TLS。勾选**开启** TLS，配置安全的路由服务。具体可参见 [配置安全的路由服务](intl.zh-CN/用户指南/Kubernetes 集群/负载均衡/Ingress 支持.md#section_j4d_jrs_vdb)。
    -   您可选择使用已有密钥。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4549_zh-CN.png)

        1.  登录 master 节点，创建tls.key和 tls.crt。

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  创建一个 secret。

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  执行命令 `kubectl get secret`，您可看到该 secret 已经成功创建。在 Web 界面可选择创建的foo.bar这个 secret。
    -   您可选择在 TLS 界面上利用已创建的 TLS 私钥和证书，一键创建 secret。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4551_zh-CN.png)

        1.  登录 master 节点，创建 tls.key和tls.crt。

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  执行 `vim tls.key` 和 `vim tls.crt` 获取生成的私钥和证书。
        3.  将证书和私钥的内容复制到 TLS 新建密钥的面板。
8.  添加标签。

    标签的作用是为 ingress 添加对应的标签，标示该 Ingress 的特点。

     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4553_zh-CN.png) 

9.  最后单击**创建**，返回路由列表。

    等待一段时间，可以看到一条路由。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4554_zh-CN.png)

10. 单击路由中的访问域名 `test.[cluster-id].[region-id].alicontainer.com`，以及 `foo.bar.com/svc2` 和 `foo.bar.com/svc3`，可访问 nginx 的欢迎页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4555_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4556_zh-CN.png)


