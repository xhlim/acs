# 子账号Kubernetes应用权限配置指导 {#concept_qlf_lv4_f2b .concept}

在通过集群访问凭证ConfigKube时，阿里云Kubernetes服务支持子账号Kubernetes应用权限配置，实现子账号应用操作权限的细粒度控制。

## 前提条件 {#section_obh_x2p_f2b .section}

-   您已有一个阿里云主账号，并拥有一个或若干个子账号，并获取子账号的UID。
-   您已开启容器服务，并创建了一个Kubernetes集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)
-   您已成功通过Kubectl连接到Kubernetes集群，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   你已成功在集群中部署一个Pod，本例中创建一个名为hello-pod的示例Pod。

## 获取子账号KubeConfig {#section_ot2_4v4_f2b .section}

**说明：** 您需要以子账号登录容器服务管理控制台，获取集群访问凭证KubeConfig。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**集群**，进行集群列表页面。
3.  选择所需的集群，单击右侧的**管理**，进入集群管理页面，复制KubeConfig的内容。
4.  进入到本机Linux环境中，将KubeConfig内容复制到本机 $HOME/.kube/config文件中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15116/6471_zh-CN.png)

5.  执行`kubectl cluster-info`命令，查看集群状态，确认集群连接正常。

    ```
    $ kubectl cluster-info
    Kubernetes master is running at https://118.178.30.xxx:6443
    Heapster is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/heapster/proxy
    KubeDNS is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    monitoring-influxdb is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/monitoring-influxdb/proxy
    
    
    ```


## 集群访问角色配置模板 {#section_q1y_pv4_f2b .section}

当前容器服务创建的集群中并没有预置定制化的集群角色，主账号管理员需要登录到部署控制台中使用模板创建的方式，或者登录到任意Master节点上通过kubectl命令手工部署需要的角色模型。

**说明：** 若通过kubectl连接集群的方式部署角色模型，您需要以主账号的KubeConfig作为登录凭证，否则无法创建集群访问角色，您将需要复制两次KubeConfig内容。建议通过容器服务控制台或SSH连接到集群的方式进行部署。

角色模型的配置方式可参考Kubernetes官方的文档[https://kubernetes.io/docs/reference/access-authn-authz/rbac/- role-and-clusterrole](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-and-clusterrole)，根据用户的使用习惯，结合容器服务自身形态，容器服务向您推荐以下几个集群角色\(ClusterRole\)模板，相关的示例编排如下：

**管理员**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:admin
rules:
- apiGroups: [""]
  resources: ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["bindings", "events", "limitranges", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status", "resourcequotas", "resourcequotas/status"]
  verbs: ["get", "list", “watch"]
- apiGroups: [""]
  resources: ["namespaces", "nodes", "persistentvolumes"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["autoscaling"]
  resources: ["horizontalpodautoscalers"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]

```

**运维人员**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:opr
rules:
- apiGroups: [""]
  resources: ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["bindings", "events", "limitranges", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status", "resourcequotas", "resourcequotas/status"]
  verbs: ["get", "list", “watch"]
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["autoscaling"]
  resources: ["horizontalpodautoscalers"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]

```

**开发人员**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:dev
rules:
- apiGroups: [""]
  resources: ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: [""]
  resources: ["events", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status"]
  verbs: ["get", "list", “watch"]
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["autoscaling"]
  resources: ["horizontalpodautoscalers"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
  verbs: ["create", "delete", "deletecollection", "get", "list", "patch", "update", "watch"]

```

**受限人员**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:restricted
rules:
- apiGroups: [""]
  resources: ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy"]
  verbs: ["get", "list", “watch"]
- apiGroups: [""]
  resources: ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
  verbs: ["get", "list", “watch"]
- apiGroups: [""]
  resources: ["events", "replicationcontrollers/status", "pods/log", "pods/status"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["autoscaling"]
  resources: ["horizontalpodautoscalers"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
  verbs: ["get", "list", “watch"]
```

## 配置子账号对应角色和角色绑定 {#section_ycv_sv4_f2b .section}

当前控制台尚不支持子账号权限的自动化配置，同样需要主账号管理员在目标集群的部署控制台中使用模板创建子账号对应的角色绑定（按需可以是集群维度的ClusterRoleBinding或是namespace维度的RoleBinding）。您也可登录到任意master节点上通过kubectl命令手工添加，

**说明：** 角色绑定中的subject kind为User，name是子账号对应的UID。

以受限人员的角色模板为例，演示如何为子账号配置一个受限人员的角色，并进行绑定，让该子账号不能进行权限外的操作。

1.  SSH登录到Master节点，执行`vim restricted-clusterrole.yaml`命令，创建restricted-clusterrole.yaml文件。

    在该文件中输入如下yaml编排。

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      name: cs:restricted
    rules:
    - apiGroups: [""]
      resources:  ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy" ]
      verbs: ["get", "list", “watch"]                  #不允许删除Pod
    - apiGroups: [""]
      resources:  ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
      verbs: ["get", "list", “watch"]
    - apiGroups: [""]
      resources:  ["events", "replicationcontrollers/status", "pods/log", "pods/status"]
      verbs: ["get", "list", “watch"]
    - apiGroups: ["apps"]
      resources:  ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
      verbs: ["get", "list", “watch"]
    - apiGroups: ["autoscaling"]
      resources:  ["horizontalpodautoscalers"]
      verbs: ["get", "list", “watch"]
    - apiGroups: ["batch"]
      resources:  ["cronjobs", "jobs"]
      verbs: ["get", "list", “watch"]
    - apiGroups: ["extensions"]
      resources:  ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
      verbs: ["get", "list", “watch"]
    ```

    执行`kubectl create -f restricted-clusterrole.yaml`命令，创建该ClusterRole。

2.  执行`vim restricted-clusterrole-binding.yaml`命令，并在该文件中输入如下yaml模板。

    ```
    kind: ClusterRoleBinding
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      name: read-pod-global
    subjects:
    - kind: User                            ##类型为User
      name: "204299326441xxxxx"            ##您的子账号用户UID
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: ClusterRole
      name: cs:restricted
      apiGroup: rbac.authorization.k8s.io
    
    
    ```

    执行`kubectl create -f restricted-clusterrole-binding.yaml`命令，创建该ClusterRoleBinding。

3.  ClusterRole和ClusterRolebinding创建完毕后，返回通过子账号ConfigKube凭证连接集群的本机环境。尝试通过kubectl delete命令删除一个Pod。

    **说明：** 本例中，受限人员的ClusterRole模板不允许被绑定该角色的子账号删除Pod。

    ```
    $ kubectl get pods
    NAME READY STATUS RESTARTS AGE
    hello-pod 1/1 Running 0 16s
    
    $ kubectl delete pod hello-pod
    Error from server (Forbidden): pods "hello-pod" is forbidden: User "204299326441xxxxxx" cannot delete pods in the namespace "default"
    
    
    
    ```


您可以看到，以子账号登录到集群时，无法对未授权的角色进行操作。

## 后续增强 {#section_dz2_c4p_f2b .section}

后续容器服务会在创建的Kubernetes集群中添加预置的集群角色，并在控制台增加权限管理页面，提供针对子账号的集群或namespace维度的角色绑定；同时提供细粒度的面向Kubernetes模型级别的子账号应用权限管理功能。

