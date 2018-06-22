# Create an Ingress in Container Service console {#concept_qm4_kyn_vdb .concept}

Alibaba Cloud Container Service console integrates with the Ingress service, which allows you to quickly create an Ingress service in the Container Service console to build the flexible and reliable traffic access layer.

## Prerequisites {#section_kj5_nyn_vdb .section}

-   You have successfully created a Kubernetes cluster and Ingress controller is running normally in the cluster. For how to create a Kubernetes cluster, see [Create a cluster](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   Log on to the master node by using SSH. For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).

## Step 1 Create a deployment and a service {#section_c1p_ryn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click**Application** \> **Deployment**in the left-side navigation pane.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4535_en-US.png)

4.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    In this example, select Resource - basic Deployment to create an Nginx deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4536_en-US.png)

5.  Under Kubernetes, click**Application** \> **Service** in the left-side navigation pane. Click **Create** in the upper-right corner.

    In this example, to test the complicated Ingress service, create multiple services and bind the services to the backend deployment, with ClusterIP as the service type.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4537_en-US.png)

    After the creation, you can view the three services created in this example on the Service List page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4538_en-US.png)


## Step 2 Create an Ingress {#section_gwv_tyn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click**Application** \> **Ingress**in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4539_en-US.png)

4.  In the displayed Create dialog box, enter the Ingress name. In this example, enter nginx-ingress.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4542_en-US.png)

5.  Configure the rules.

    The Ingress rules are the rules that authorize the inbound access to the cluster and are generally the HTTP rules. Configure the domain name \(virtual hostname\), URL path, service name, and port. For more information, see [Ingress configurations](reseller.en-US/User Guide/Kubernetes cluster/Ingress/Ingress configurations.md#).

    In this example, add a complicated Ingress rule. Configure the default testing domain name and virtual hostname of the cluster to display the Ingress service based on the domain names.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4543_en-US.png)

    -   The simple Ingress based on the default domain name, that is, provide the access service externally by using the default domain name of the cluster.
        -   **Domain**: Enter the default domain name of the cluster. In this example, use `test.[cluster-id].[region-id].alicontainer.com`.

            The default domain name of this cluster is displayed in the Create dialog box, with the format of `*.[cluster-id].[region-id].alicontainer.com`. You can also obtain the default domain name on the Basic Information page of the cluster.

        -   **Service**: Configure the access path, name, and port of the service.
            -   Path: Specify the URL path of the service access, for example,/svc1. Each path is associated with a backend service. Before Alibaba Cloud Server Load Balancer forwards the traffic to the backend, all inbound requests must match with the domain name and path.
            -   Name and Port: The backend configuration, which is a combination of `service:port`, for example, `http-svc1:80`.  The Ingress traffic is forwarded to the matched backend services.
    -   The simple fanout Ingress based on the domain name. In this example, use a virtual hostname as the testing domain name to provide the access service externally. You can use the recorded domain name in the production environment to provide the access service.
        -   **Domain**: In this example, use the testing domain name `foo.bar.com`.

            You must modify the hosts file to  add a domain name mapping rule.

            ```
            118.178.108.143 foo.bar.com
            ```

        -   **Service**: Configure the access path, name, and port of the service.
            -   Path: Specify the URL path of the service access, such as /svc2 and /svc3.
            -   Name and Port: Specify `http-svc2:80` and `http-svc3:80` in this example.
6.  Configure the annotations.

    Click **rewrite annotation** to add a typical rewrite annotation for Ingress, that is, `nginx.ingress.kubernetes.io/rewrite-target: /`, which indicates to redirect the path /pathto the path / that can be recognized by the backend services.

    You can also click **Add** to enter the name and value of the annotation, namely, the annotation key-value pair of Ingress. For more information about the common rewrite annotations, see [rewrite annotations](https://github.com/kubernetes/ingress-nginx/tree/master/docs/examples/rewrite).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4544_en-US.png)

7.  Configure TLS. Select **Enable** and configure the secure Ingress service. For more information, see [Configure a safe routing service](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#section_j4d_jrs_vdb).
    -   You can select to use an existing secret.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4549_en-US.png)

        1.  Log on to the master node to create the tls.key and tls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Create a secret.

            ```
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  Run the command  `kubectl get secret` and then the secret is successfully created. Select the created secret foo.bar in the Container Service console.
    -   You can create the secret with one click by using the created TLS private key and certificate.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4551_en-US.png)

        1.  Log on to the master node to create the tls.key andtls.crt.

            ```
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  Run the commands `vim tls.key` and `vim tls.crt` to obtain the generated private key and certificate.
        3.  Copy the generated certificate and private key to the Cert and Key fields.
8.  Click Add to add a tag.

    Add the corresponding tags for Ingress to indicate the characteristics of the Ingress.

     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4553_en-US.png) 

9.  Click **Create**.

    The Ingress nginx-ingress is displayed on the Ingress page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4554_en-US.png)

10. Click the access domain name `test.[cluster-id].[region-id].alicontainer.com`, `foo.bar.com/svc2`, and `foo.bar.com/svc3` to access the Nginx welcome page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4555_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6914/4556_en-US.png)


