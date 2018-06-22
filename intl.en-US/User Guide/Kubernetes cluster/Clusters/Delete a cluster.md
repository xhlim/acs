# Delete a cluster {#task_kh11_5st_4y .task}

In the Container Service console, you can delete clusters that are no longer in use.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.   Click Kubernetes \> **Clusters** in the left-side navigation pane. 
3.   Click**More at the right of the cluster and then select** \> **Delete**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/4338_en-US.png)


**Failed to delete a cluster**

If you manually add some resources under the resources created by Resource Orchestration Service \(ROS\), ROS does not have permissions to delete these manually added resources. For example, manually add a VSwitch under the Virtual Private Cloud \(VPC\) created by ROS. ROS fails to process this VPC when deleting the Kubernetes resources and then the cluster fails to be deleted.

Container Service allows you to force delete the cluster. You can force delete the cluster record and ROS stack if the cluster fails to be deleted. However, you must release the manually created resources manually.

The cluster status is Failed to delete if the cluster fails to be deleted.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/4339_en-US.png)

Click**More at the right of the cluster and then select** \> **Delete.**In the displayed dialog box, you can see the resources that failed to be deleted. Select the **Force Delete** check box and then click **OK** to delete the cluster and ROS stack.

**Note:** You must manually release the resources that failed to be deleted. To find these resources, see [Failed to delete Kubernetes clusters: ROS stack cannot be deleted](reseller.en-US/User Guide/Kubernetes cluster/FAQ/Failed to delete Kubernetes clusters: ROS stack cannot be deleted.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/4340_en-US.png)

