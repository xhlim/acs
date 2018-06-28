# 基于容器 HUB 的持续交付 {#task_jfz_1m5_xdb .task}

介绍如何通过添加触发器和设置 Webhook 实现自动重新部署应用。

假设您已经有一个部署在阿里云容器服务上的 nodejs 应用。该应用代码托管在 GitHub 中，镜像仓库使用的是阿里云 Docker Hub，镜像仓库设置了代码变更时自动构建镜像的构建设置。

确认更新。

1.  Docker Hub 中的镜像如下所示。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5446_zh-CN.jpg)

2.  初始的编排模板如下所示。 

    ```
    nodejs-demo:
     image: 'registry.cn-hangzhou.aliyuncs.com/qinyujia-test/nodejs-demo'
     expose:
         - '22'
         - '3000'
     restart: always
     labels:
         aliyun.routing.port_3000: nodejs-demo
    ```

3.  应用如下所示。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5447_zh-CN.jpg)

4.  为 nodejs 应用创建重新部署类型的触发器。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5449_zh-CN.jpg)

5.  在 Docker Hub 中添加一条 Webhook 记录，将上一步创建的触发器链接填写到 Webhook URL 中。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5450_zh-CN.jpg)

    此时在 GitHub 中提交代码变更，镜像就会自动重新构建，应用会自动重新部署。

6.  确认更新。 
    -   通过查看应用的事件来确认更新。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5451_zh-CN.jpg)

    -   通过 Webhook 的访问历史来确认更新。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7100/5452_zh-CN.jpg)


