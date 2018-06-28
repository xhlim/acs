# Set the root domain name of a cluster {#task_oh1_ppl_xdb .task}

When you[Create an Nginx webserver from an image](../../../../intl.en-US/Quick Start/Swarm cluster/Create an Nginx webserver from an image.md#) and configure the web routing rules, you are only required to enter the domain name prefix `nginx`. Then, you can obtain `the domain name in the format of` $cluster\_id.$region\_id.alicontainer.com. You can replace this domain name by setting a root domain name \(`51ili.com` is used in this example\) of the cluster. When you redeploy the application `nginx`, the domain name changes from `nginx.cd5b226071936493b89e75bbe8841664c.cn-hangzhou.alicontainer.com to` `nginx.51ili.com`, which makes it convenient for you to access the cluster applications with your own root domain name.

**Note:** To guarantee the normal operation of the following example, upgrade the Agent to the latest version first.

1.   Bind a Server Load Balancer instance. 
    1.   Log on to the [Container Service console](https://cs.console.aliyun.com/). 
    2.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
    3.   Click **Clusters** in the left-side navigation pane. 
    4.   Click **Manage** at the right of the cluster \(`routing-test-online` in this example\) that you want to configure. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6997/4811_en-US.png)

    5.   Click **Load Balancer Settings** in the left-side navigation pane. 

        If no Server Load Balancer instance is bound to this cluster, log on to the [Server Load Balancer console](https://slbnext.console.aliyun.com/) and create a Server Load Balancer instance. Then, return to this page and bind the instance to this cluster.

        **Note:** For more information about how to bind and unbind a Server Load Balancer instance to and from a cluster and the limits in Container Service, see [Bind and unbind a Server Load Balancer instance](intl.en-US/User Guide/Swarm cluster/Clusters/Bind and unbind a Server Load Balancer instance.md#).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6997/4812_en-US.png)

2.   Set the domain name. 
    1.   Click the Set Domain Name tab and enter the root domain name you bought in the Domain Name field. In this example, 51ili.com is entered. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6997/4813_en-US.png)

    2.   Click **Set**. 
3.   Resolve the domain name to the bound Server Load Balancer instance. 
    1.   Log on to the Server Load Balancer console. Click Instances in the left-side navigation pane, and then click the ID of the Server Load Balancer instance bound to the cluster routing-test-online. 
    2.   View the instance details. Find the instance IP address. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6997/4814_en-US.png)

    3.   Log on to the Alibaba Cloud DNS console and add record A to resolve `*.51ili.com` to the Server Load Balancer VIP address. 

        ![](images/4815_en-US.png)

4.   Redeploy the nginx application. 
    1.   Click Redeploy at the right of nginx. The service access endpoint of the application nginx is changed. 

        The access endpoint before setting the root domain name.

        ![](images/4817_en-US.png)

        The access endpoint after setting the root domain name.

        ![](images/4818_en-US.png)

    2.   Access the latest access endpoint http://nginx.51ili.com. 

        ![](images/4820_en-US.png)


