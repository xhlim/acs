# Architecture {#concept_jzv_5t5_tdb .concept}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6856/966_en-US.png)

The basic architecture of Container Service is as shown in the preceding figure, and is described as follows:

-   **Cluster management service:** Docker cluster management and scheduling are supported.
-   **Service discovery:** Storage of metadata \(including Docker status\) is supported.
-   **Agent communication service:** Communication service between each host and cluster management service is supported.
-   **Cluster API:** United APIs of Alibaba Cloud are provided.
-   **Service API:** APIs that are compatible with Docker Swarm APIs are provided.

