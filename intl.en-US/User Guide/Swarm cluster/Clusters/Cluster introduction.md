# Cluster introduction {#concept_otb_zt2_xdb .concept}

A cluster is a collection of cloud resources that are required to run containers. It is associated with several Elastic Compute Service \(ECS\) nodes, Server Load Balancer, and other cloud resources.

## Create a cluster {#section_qr4_w52_xdb .section}

You can create a cluster by using the following methods:

**Method 1:**  Create a cluster and several ECS instances.

You can directly create a cluster with several new ECS instances by using Container Service. 

For more information, see [Create a cluster](reseller.en-US/User Guide/Swarm cluster/Clusters/Create a cluster.md#).

The ECS instances created using this method are all Pay-As-You-Go instances. If you want to use monthly or yearly subscription ECS instances, buy them separately and then follow **Method 2**.

**Method 2:** Create a zero-node cluster and add existing ECS instances to the cluster.

1.  Create a zero-node cluster.

    If you have purchased several ECS instances from the ECS service, create a zero-node cluster in Container Service. Method 1 except that you need to select Do not Add when creating the cluster to add existing ECS instances instead of creating some new ones.

    The operations are the same as  **Method 1** except that you need to select **Do not Add** when creating the cluster to  add existing ECS instances instead of creating some new ones.

2.  Add existing ECS instances.

    You can add an existing ECS instance to Container Service in the following ways:

    -   Reset the image of the ECS instance and add the ECS instance to the cluster automatically. 

        As this method will reset the image and system disk of the ECS instance, proceed with caution.  However, ECS instances added by using this method are cleaner.

    -   Run scripts on the ECS instance and manually add the ECS instance to the cluster.

        This method is applicable to images that do not require a reset of the ECS instance.

     [Add an existing ECS instance](reseller.en-US/User Guide/Swarm cluster/Clusters/Add an existing ECS instance.md#).


## Manage a cluster {#section_ur4_w52_xdb .section}

You can search for, expand, connect to, clean up, or delete a cluster. For more information, see the following documents:

-   [Search for a cluster](reseller.en-US/User Guide/Swarm cluster/Clusters/Search for a cluster.md#)
-   [Expand a cluster](reseller.en-US/User Guide/Swarm cluster/Clusters/Expand a cluster.md#)
-   [Download cluster certificate](reseller.en-US/User Guide/Swarm cluster/Clusters/Download cluster certificate.md#)
-   [Clean up a cluster disk](reseller.en-US/User Guide/Swarm cluster/Clusters/Clean up a cluster disk.md#)
-   [Delete a cluster](reseller.en-US/User Guide/Swarm cluster/Clusters/Delete a cluster.md#)

