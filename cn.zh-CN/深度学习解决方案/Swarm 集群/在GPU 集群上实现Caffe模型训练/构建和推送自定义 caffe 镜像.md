# 构建和推送自定义 caffe 镜像 {#concept_w32_5gm_vdb .concept}

目前，深度学习解决方案还未内置对 caffe 框架的支持，但是训练框架支持自定义镜像，提供了灵活性。您可以通过指定自定义镜像的方式，使用您自己的 caffe 框架来进行模型训练。

您可以使用指定的 caffe 框架制作成 Docker 镜像，推送到容器镜像仓库中，如阿里云容器镜像仓库。以后在该集群部署的训练任务就可以使用这个 caffe 镜像。在开通容器服务的同时，也会开通阿里云容器镜像仓库服务。

## 前提条件 {#section_nyh_bhm_vdb .section}

1.  需要准备一个容器镜像镜像仓库，来推送生成的镜像。可以使用与容器集群相同地域的阿里云容器镜像仓库，或者私有的容器镜像仓库。本例中使用阿里云容器镜像仓库服务。
2.  需要一个可以构建 Docker 镜像的环境，且这个环境需要能 push 镜像到镜像仓库。本例中以前面创建的 swarmcluster 容器集群的一个 ECS 节点作为构建 Docker 镜像的环境。

## 构建容器镜像仓库 {#section_or2_2hm_vdb .section}

1.  登录 [容器镜像服务管理控制台](https://cr.console.aliyun.com/)。
2.  单击右上角 **创建镜像仓库**。进行相关参数配置，详情请参见 [构建容器镜像](../../../../cn.zh-CN/用户指南/Swarm 集群/构建管理/构建容器镜像.md#) 中关于创建镜像仓库的内容。
3.  本示例里，在华东1区创建镜像仓库 **registry.cn-hangzhou.aliyuncs.com/dl-framework/acs-caffe**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7437/15330281842205_zh-CN.png)


## 在节点上构建 dockerfile 和脚本文件 {#section_gkn_ygm_vdb .section}

选择容器集群中的一台 ECS 实例，登录到该节点上。在该节点上创建一个目录，用于存放将要创建的 dockerfile 文件和脚本文件。

**创建dockerfile文件**

您可以在集群中的一个 ECS 节点上创建 custom\_train\_caffe.dockerfile 文件。

登录到 ECS 节点上，依次执行以下命令，开始创建 dockerfile。

```
mkdir caffe-file
cd  caffe-file
vim custom_train_caffe.dockerfile    ##创建dockerfile文件
```

然后输入如下的示例内容。

```
FROM bvlc/caffe:gpu
RUN  apt-get update && apt-get install -y git vim
RUN  mkdir /starter
COPY ./custom_train_helper.sh /starter
WORKDIR /starter
RUN  chmod +x custom_train_helper.sh
ENTRYPOINT ["./custom_train_helper.sh"]
```

该镜像基于 caffe 官方基础镜像 `bvlc/caffe:gpu`， 并使用一个自定义的脚本 `custom_train_helper.sh` 作为用镜像启动容器时的入口进程。

保存后，成功创建 dockerfile 文件。

**创建脚本文件**

在相同目录下创建 custom\_train\_helper.sh 文件供 dockerfile 文件里构建镜像时使用，创建脚本文件与上面的方式类似，脚本的内容如下：

```
#!/bin/bash
default_output_path='/output'
default_remote_volume_path=$DEFAULT_REMOTE_VOLUME_PATH
default_input_path='/input'
# default input dir
if [ -d "$default_remote_volume_path" ] && [ ! -z $default_remote_volume_path ]; then
  ln -s $default_remote_volume_path $default_input_path
fi
# default output dir
if [ ! -d "$default_output_path" ]; then
  mkdir -p $default_output_path
fi
# exec user's command
eval "$@"
echo "Done training."
# auto persist outputs to user's remote volume
if [ -d $default_remote_volume_path ] && [ ! -z $default_remote_volume_path ]; then
  cp -r $default_output_path/ $default_remote_volume_path
else
  echo "Cannot find remote data volume $default_remote_volume_path, checkpoints are not persisted remotely."

fi
```

脚本逻辑主要是在执行具体训练命令的前后期，设置工作目录，和训练日志、结果的备份工作。

创建完毕后，执行下面的命令查看是否创建成功。

```
[root@xxx]# ls -l
-rw-r--r-- 1 root root 396 1月  15 11:22 custom_train_caffe.dockerfile
-rw-r--r-- 1 root root 952 1月  15 11:16 custom_train_helper.sh
```

## 构建镜像和推送镜像到镜像仓库中 {#section_skn_ygm_vdb .section}

**构建镜像**

在同级目录下构建自定义镜像。

```
docker build -t registry-vpc.cn-hangzhou.aliyuncs.com/dl-framework/acs-caffe:gpu -f custom_train_caffe.dockerfile .
```

执行命令 docker images 查看是否成功构建镜像。

```
[root@xxx]# docker images
REPOSITORY                                                             TAG                 IMAGE ID            CREATED             SIZE
registry-vpc.cn-hangzhou.aliyuncs.com/dl-framework/acs-caffe           gpu                 7a2b58e3c5fb        39 seconds ago      3.49GB
```

**将镜像推送到镜像仓库中**

接下来将构建好的镜像 registry-vpc.cn-hangzhou.aliyuncs.com/dl-framework/acs-caffe:gpu 推送到之前在华东1区创建的镜像仓库中去。可以参考[镜像基本操作](https://help.aliyun.com/document_detail/60743.html)。

首先登录到镜像仓库，如下：

```
[root@xxx]# docker login --username=user@aliyun.com registry-vpc.cn-hangzhou.aliyuncs.com  ##注意替换为用户登录账号
Password:      ##注意该密码是镜像仓库的独立登录密码
Login Succeeded
```

然后将镜像 push 到镜像仓库。

```
docker push registry-vpc.cn-hangzhou.aliyuncs.com/dl-framework/acs-caffe:gpu
```

您可以在 [容器镜像服务控制台](https://cr.console.aliyun.com/) 查看该镜像仓库。可以查看到刚刚推送的 caffe 镜像的公网、内网地址。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7437/15330281842206_zh-CN.png)

您可以单击 **镜像版本**，查看镜像的基本信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7437/15330281842207_zh-CN.png)

