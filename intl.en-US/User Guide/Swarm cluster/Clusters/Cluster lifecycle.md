# Cluster lifecycle {#concept_fqv_1v2_xdb .concept}

|Status|Description|
|------|-----------|
|Inactive|The successfully created cluster does not contain any node.|
|Initial|The cluster is applying for corresponding cloud resources.|
|Running|The cluster successfully applied for the cloud resources.|
|Updating|The cluster is upgrading the Agent.|
|Scaling|Change the number of cluster nodes.|
|Failed|The cluster application for cloud resources failed.|
|Deleting|The cluster is being deleted.|
|DeleteFailed|The cluster failed to be deleted.|
|Deleted \(invisible to users\)|The cluster is successfully deleted.|

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6989/4752_en-US.png "Cluster status flow")

