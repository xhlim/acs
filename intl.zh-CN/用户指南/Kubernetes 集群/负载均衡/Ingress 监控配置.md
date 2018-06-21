# Ingress 监控配置 {#concept_nmh_nks_vdb .concept}

您可以通过开启 Ingress 默认 VTS 模块来查看 Ingress 监控数据。

## 通过命令行操作 {#section_chy_qqs_vdb .section}

1.  修改 Ingress ConfigMap 配置，增加配置项 `enable-vts-status: "true"`。

    ```
    root@master # kubectl edit configmap nginx-configuration -n kube-system
    configmap "nginx-configuration" edited
    ```

    修改后 Ingress ConfigMap 内容如下。

    ```
    apiVersion: v1
    data:
      enable-vts-status: "true" # 开启VTS模块
      proxy-body-size: 20m
    kind: ConfigMap
    metadata:
      annotations:
        kubectl.kubernetes.io/last-applied-configuration: |
          {"apiVersion":"v1","data":{"proxy-body-size":"20m"},"kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app":"ingress-nginx"},"name":"nginx-configuration","namespace":"kube-system"}}
      creationTimestamp: 2018-03-20T07:10:18Z
      labels:
        app: ingress-nginx
      name: nginx-configuration
      namespace: kube-system
      selfLink: /api/v1/namespaces/kube-system/configmaps/nginx-configuration
    ```

2.  验证 Ingress Nginx 正常开启 VTS 模块。

    ```
    root@master # kubectl get pods --selector=app=ingress-nginx -n kube-system
    NAME                                        READY     STATUS    RESTARTS   AGE
    nginx-ingress-controller-79877595c8-78gq8   1/1       Running   0          1h
    root@master # kubectl exec -it nginx-ingress-controller-79877595c8-78gq8 -n kube-system -- cat /etc/nginx/nginx.conf | grep vhost_traffic_status_display
             vhost_traffic_status_display;
             vhost_traffic_status_display_format html;
    ```

3.  本地访问 Ingress Nginx 监控控制台。

    **Note:** 鉴于安全考虑，默认 VTS Port 并不对外开放，这里通过 port-forward 方式来访问。

    ```
    root@master # kubectl port-forward nginx-ingress-controller-79877595c8-78gq8 -n kube-system 18080
    Forwarding from 127.0.0.1:18080 -> 18080
    Handling connection for 18080
    ```

4.  通过 `http://localhost:18080/nginx_status` 来访问 VTS 监控控制台。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6931/4648_zh-CN.png)


## 通过 Kubernetes Dashboard 操作 {#section_g4b_sqs_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，选择所需的集群并单击集群右侧的**控制台**，进入 Kubernetes Dashboard 页面。
3.  编辑 kube-system 命名空间下的配置字典 nginx-configuration，增加配置项 `enable-vts-status: "true"`。

    保存后 Ingress ConfigMap 内容如下。

    ```
    {
      "kind": "ConfigMap",
      "apiVersion": "v1",
      "metadata": {
        "name": "nginx-configuration",
        "namespace": "kube-system",
        "selfLink": "/api/v1/namespaces/kube-system/configmaps/nginx-configuration",
        "creationTimestamp": "2018-03-20T07:10:18Z",
        "labels": {
          "app": "ingress-nginx"
        },
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"data\":{\"proxy-body-size\":\"20m\"},\"kind\":\"ConfigMap\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"ingress-nginx\"},\"name\":\"nginx-configuration\",\"namespace\":\"kube-system\"}}\n"
       }
     },
     "data": {
       "proxy-body-size": "20m",
       "enable-vts-status": "true"
     }
    }
    ```

4.  本地访问 Ingress Nginx 监控控制台。

    **Note:** 鉴于安全考虑，默认 VTS Port 并不对外开放，这里通过 port-forward 方式来访问。

    ```
    root@master # kubectl port-forward nginx-ingress-controller-79877595c8-78gq8 -n kube-system 18080
    Forwarding from 127.0.0.1:18080 -> 18080
    Handling connection for 18080
    ```

5.  通过 `http://localhost:18080/nginx_status` 来访问 VTS 监控控制台。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6931/4648_zh-CN.png)


