# Upgrade a cluster {#concept_zf5_tng_vdb .concept}

You can upgrade the Kubernetes version of your cluster in the Container Service console.

View the Kubernetes version of your cluster in the Kubernetes cluster list.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6886/4329_en-US.png)

## Instructions {#section_udz_43w_ydb .section}

-   To upgrade the cluster, make sure your machine can access the Internet to download the necessary software packages.
-   The upgrade may fail. We recommend that you back up snapshots before upgrading the cluster to guarantee your data security. For how to create a snapshot, see [Create snapshots](../../intl.en-US/User Guide/Snapshots/Create snapshots.md#).
-   During the upgrade, the services deployed on the cluster will not be interrupted, but we recommend that you do not perform cluster operations by using kubctl or the Container Service console during the upgrade. The upgrade lasts 5–30 minutes. The cluster status changes to Running after the upgrade.

## Preparations {#section_ylf_vng_vdb .section}

Check the health status of the cluster before upgrading the cluster. Make sure the cluster is healthy.

Log on to the master node. For more information, see [EN-US\_TP\_6882.md\#](intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#) and [Connect to a Kubernetes cluster by using kubectl](intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#).

1.  Run the command `kubectl get cs` to make sure all the modules are healthy.

    ```
    NAME STATUS MESSAGE ERROR
     scheduler Healthy ok
     controller-manager Healthy ok
     etcd-0 Healthy {"health": "true"}
     etcd-1 Healthy {"health": "true"}
     etcd-2 Healthy {"health": "true"}
    ```

2.  Run the command `kubectl get nodes`  to make sure all the nodes are in the Ready status.

    ```
    kubectl get nodes
     NAME STATUS ROLES AGE VERSION
     cn-shanghai.i-xxxxxx Ready master 38d v1.9.3
     cn-shanghai.i-xxxxxx Ready <none> 38d v1.9.3
     cn-shanghai.i-xxxxxx Ready <none> 38d v1.9.3
     cn-shanghai.i-xxxxxx Ready <none> 38d v1.9.3
     cn-shanghai.i-xxxxxx Ready master 38d v1.9.3
     cn-shanghai.i-xxxxxx Ready master 38d v1.9.3
    ```


If nodes are abnormal, you can fix them by yourself or open a ticket to ask Alibaba Cloud engineers to fix them.

## Procedure {#section_fmf_vng_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  Click Kubernetes \> **Clusters** in the left-side navigation pane.
4.  Click**More at the right of the cluster to be upgraded and then select** \> **Upgrade Cluster from the drop-down list** .

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6886/4332_en-US.png)

5.  Click **Upgrade** in the displayed dialog box.

    ![](images/4333_en-US.png)

    The system starts to upgrade the Kubernetes version.

    ![](images/4335_en-US.png)

    ![](images/4336_en-US.png)


After the upgrade, you can check the Kubernetes version of this cluster in the Kubernetes cluster list to make sure whether or not the upgrade is successful.

![](images/4337_en-US.png)

