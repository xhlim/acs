# Sub-account Kubernetes permission configuration guide {#concept_qlf_lv4_f2b .concept}

When using a cluster access KubeConfig certificate, Alibaba Cloud Kubernetes supports the Kubernetes permission configuration, implementing fine-grained control over application permissions of subaccount.

## Prerequisite {#section_obh_x2p_f2b .section}

-   You have an Alibaba Cloud account, and you have also created one or several sub-accounts, and get the corresponding UID.
-   You have activated Container Service and created a Kubernetes cluster, see Create a Kubernetes cluster.[Create a cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#)
-   You have connected to a Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#).
-   You have deployed a pod in the cluster. In this document, an example pod name is hello-pod.

## Get the sub-account KubeConfig {#section_ot2_4v4_f2b .section}

**Note:** Log on to the Container Service console with a sub-account to get the cluster access KubeConfig certificate.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click Kubernetes \> **Clusters** \> in the left-side navigation pane to go to the Cluster List page.
3.  Select the cluster, click **Manage** on the right to go to the Cluster Managemenet page, and copy the contents of KubeConfig.
4.  Enter the Linux environment and copy the KubeConfig content to the $HOME/.kube/config file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15116/6471_zh-CN.png)

5.  Run the `kubectl cluster-info` command to view the cluster status and verify that the cluster connection is normal.

    ```
    $ kubectl cluster-info
    Kubernetes master is running at https://118.178.30.xxx:6443
    Heapster is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/heapster/proxy
    KubeDNS is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    monitoring-influxdb is running at https://118.178.30.xxx:6443/api/v1/namespaces/kube-system/services/monitoring-influxdb/proxy
    
    
    ```


## Cluster access role configuration template {#section_q1y_pv4_f2b .section}

If no pre-configured role exists in the current cluster created by the Container Service, use the primary account to log on to the Management console and create a template. You can also log on to the master node, and manually deploy the role using the kubectl command.

**Note:** If you deploy the role by kubectl connected to the cluster, you must use the KubeConfig of the primary account as the logon credentials. Otherwise, you cannot create the cluster access role. Therefore, you must copy the KubeConfig twice. We recommend that you use Container Service console or SSH connect to the cluster to perform the deployment.

For more information about the role configuration, see the official Kubernetes documentation [https://kubernetes.io/docs/reference/access-authn-authz/rbac/- role-and-clusterrole](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-and-clusterrole). Combine Container Service templates according to your requirements. Container Service provides the following cluster role templates \(ClusterRole\). The sample layouts are as following.

**Administrator **

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
  resources: ["bindings", "events", "limitranges", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status", "resourcequotas", "resourcequotas/status", "componentstatuses"]
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

**O&M Personnel**

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
  resources: ["bindings", "events", "limitranges", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status", "resourcequotas", "resourcequotas/status", "componentstatuses"]
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

**Developer**

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
  resources: ["events", "namespaces/status", "replicationcontrollers/status", "pods/log", "pods/status", "componentstatuses"]
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

**Restricted personnel**

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
  resources: ["events", "replicationcontrollers/status", "pods/log", "pods/status", "componentstatuses"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale", "replicasets", "replicasets/scale", "statefulsets"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["autoscaling"]
  Resources: ["horizontalpodautoscalers"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["get", "list", “watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "deployments/rollback", "deployments/scale","ingresses","replicasets", "replicasets/scale", "replicationcontrollers/scale"]
  verbs: ["get", "list", “watch"]
```

## Configure a sub-account for corresponding role and role binding {#section_ycv_sv4_f2b .section}

Currently, console does not support the automatic configuration of sub-account permissions. The primary account administrator must use the template to create the corresponding role binding for the sub-account in the Management console of the target cluster \(for example, ClusterRoleBinding for the cluster, or the RoleBinding for the namespace\). You can also log on to the master node and manually add the kubectl command,

**Note:** the subject kind in the role binding is User, and the name is the sub-account UID.

Take the role template for the restricted personnel as an example, configure and bind the role of a restricted personnel for a sub-account. Therefore, the sub-account cannot perform operations other than permitted.

1.  SSH logs on to the master node, than runs the `vim restricted-clusterrole.yaml` command, and creates the restricted-clusterrole.yaml file.

    Enter the following yaml layout in the file.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      name: cs:restricted
    rules:
    - apiGroups: [""]
      resources: ["pods", "pods/attach", "pods/exec", "pods/portforward", "pods/proxy"]
      verbs: ["get", "list", "watch"] # No pod allowed to be deleted
    - apiGroups: [""]
      resources: ["configmaps", "endpoints", "persistentvolumeclaims", "replicationcontrollers", "replicationcontrollers/scale", "secrets", "serviceaccounts", "services", "services/proxy"]
      verbs: ["get", "list", “watch"]
    - apiGroups: [""]
      resources: ["events", "replicationcontrollers/status", "pods/log", "pods/status", "componentstatuses"]
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

    Run the `kubectl create -f restricted-clusterrole.yaml` command to create the ClusterRole.

2.  Run the `vim restricted-clusterrole-binding.yaml` command, and enter the following yaml template in the file.

    ```
    kind: ClusterRoleBinding
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      name: read-pod-global
    subjects:
    -Kind: user # Type is user
      name: "204299326441xxxxx" ##Your sub-account UID
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: ClusterRole
      name: cs:restricted
      apiGroup: rbac.authorization.k8s.io
    
    
    ```

    Run the `kubectl create -f restricted-clusterrole-binding.yaml` to create the ClusterRoleBinding.

3.  After the ClusterRole and ClusterRoleBinding have been created, return to the native environment that is connected to the cluster by the sub-account KubeConfig. Try to delete the pod with the kubectl delete command.

    **Note:** In this example, the restricted personnel ClusterRole template does not allow the sub-account bound to the role to delete the pod.

    ```
    $ kubectl get pods
    NAME READY STATUS RESTARTS AGE
    hello-pod 1/1 Running 0 16s
    
    $ kubectl delete pod hello-pod
    Error from server (Forbidden): pods "hello-pod" is forbidden: User "204299326441xxxxxx" cannot delete pods in the namespace "default"
    
    
    
    ```


When log on to the cluster with a sub-account, you cannot operate on the unauthorized roles.

## Subsequent enhancements  {#section_dz2_c4p_f2b .section}

Preset cluster roles in the created Kubernetes cluster and permission management page in the console, providing role binding for clusters or namespaces, are going to be added to Container Service. At the same time fine-grained Kubernetes model-level sub-account permission management is also to be provided.

