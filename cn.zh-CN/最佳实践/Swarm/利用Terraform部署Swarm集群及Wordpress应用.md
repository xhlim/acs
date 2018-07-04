# 利用Terraform部署Swarm集群及Wordpress应用 {#concept_fpj_ty1_ydb .concept}

本文档介绍了如何通过 Terraform 在 VPC 环境下部署一个阿里云容器服务集群，并在该集群之上，部署一个 WordPress 样例应用。本文档提供一种构建阿里云基础设施的解决方案，让您通过代码来自动创建、编排和管理容器服务以及完成在容器集群上应用的自动化部署。

## 前提条件 {#section_i5h_gz1_ydb .section}

-   需要开通阿里云容器服务。
-   需要启用用户账号的 AccessKey，妥善保存和记录 AccessKey ID 和 AccessKey Secret。

## 步骤 1 安装 Terraform {#section_jfy_gz1_ydb .section}

**下载 Terraform**

在 [Terraform 官方下载地址](https://www.terraform.io/downloads.html) 下载 Terraform您可以选择合适的版本和平台。本文档以在 Linux 上安装 Terraform 为例（操作步骤与 Mac OS X 平台十分相似）。

1.  单击 Linux 图标下载 `terraform_0.11.3_linux_amd64.zip` 文件。
2.  复制该 .zip文件到合适的路径中，本例中为 /usr/local/terraform。
3.  解压缩该文件，您会得到一个二进制文件 terraform。
4.  在/etc/profile下创建以下条目，将二进制文件所在的路径/usr/local/terraform 添加到 PATH 环境变量中。

    ```
    export TERRAFORM_HOME=/usr/local/terraform
    export PATH=$PATH:$TERRAFORM_HOME
    ```


**安装阿里云 Terraform Provider**

 在使用Terraform之前，需要进行初始化操作来加载面向阿里云的Provider。在模板文件目录下运行命令：

```
terraform init
```

加载成功后，将会把相应的 Plugin 下载到当前文件夹下的.terraform隐藏目录下。如果在加载过程中遇到了网络超时的问题，可按照接下来的步骤完成插件的手动安装。

-   在[阿里云 Terraform Provider 官方下载地址](https://releases.hashicorp.com/terraform-provider-alicloud/)下载对应版本和平台的 Provider。本例中选择 Linux 类型。
-   复制下载的文件 terraform-provider-alicloud\_1.9.3\_linux\_amd64.zip到 Terraform 的安装目录 /usr/local/terraform并解压即可。将解压后，当前目录将会得到阿里云的 Provider terraform-provider-alicloud\_v1.9.3\_x4。

Terraform 和 Provider 安装成功后，运行以下命令检测 Terraform 的运行，若成功安装，应显示以下内容。

```
$ terraform
Usage: terraform [--version] [--help]  [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.

Common commands:
....

All other commands:
debug Debug output management (experimental)
force-unlock Manually unlock the terraform state
state Advanced state management
```

## 步骤2 下载容器服务 Swarm 和应用 Wordpress 的 Terraform 模板 {#section_o1j_gt5_c2b .section}

您可以从 GitHub上下载创建 Swarm 集群和部署 Wordpress 应用的 Terraform 模板（[模板下载地址](https://github.com/terraform-providers/terraform-provider-alicloud/tree/master/examples/swarm-wordpress)）。该模板文件定义了创建 swarm 集群的相关资源以及在 swarm 集群上部署 Wordpess 的文件，帮助您完成 Swarm 集群的快速创建和应用的快速部署。 模板中包含以下文件。

**main.tf**

Terraform 主文件。定义了将要部署的资源。

-   **地域** 

    定义了资源将要被创建在哪个地域里。

    ```
    provider "alicloud" {
    access_key = "${var.alicloud_access_key}"
    secret_key = "${var.alicloud_secret_key}"
    region = "${var.region}"
    }
    ```

-   **VPC**

    ```
    resource "alicloud_vpc" "vpc" {
    name = "${var.vpc_name}"
    cidr_block = "${var.vpc_cidr}"
    }
    ```

-   **VSwitch**

    ```
    resource "alicloud_vswitch" "vswitch" {
    availability_zone = "${data.alicloud_zones.default.zones.0.id}"
    name = "${var.vswitch_name}"
    cidr_block = "${var.vswitch_cidr}"
    vpc_id = "${alicloud_vpc.vpc.id}"
    }
    ```

-   **容器服务集群**

    ```
    resource "alicloud_cs_swarm" "cs_vpc" {
    password = "${var.password}"
    instance_type = "${data.alicloud_instance_types.main.instance_types.0.id}"
    name = "${var.cluster_name}"
    node_number = "${var.node_number}"
    disk_category = "${var.disk_category}"
    disk_size = "${var.disk_size}"
    cidr_block = "${var.cidr_block}"
    image_id = "${data.alicloud_images.main.images.0.id}"
    vswitch_id = "${alicloud_vswitch.main.id}"
    }
    ```

-   **Wordpress 应用**

    ```
    resource "alicloud_cs_application" "wordpress" {
    cluster_name = "${alicloud_cs_swarm.cs_vpc.name}"
    name = "${var.app_name == "" ? var.resource_group_name : var.app_name}"
    version = "${var.app_version}"
    template = "${file("wordpress.yml")}"
    description = "terraform deploy consource"
    latest_image = "${var.latest_image}"
    blue_green = "${var.blue_green}"
    blue_green_confirm = "${var.confirm_blue_green}"
    }
    ```


**outputs.tf**

 该文件定义了输出参数。作为执行的一部分而创建的资源会生成这些输出参数。和 ROS 模板指定的输出参数类似。例如，该模板将部署一个 Swarm 集群和 Wordpress 应用实例。以下输出参数将提供集群 ID 和应用的默认域名。

```
output "cluster_id" {
  value = "${alicloud_cs_swarm.cs_vpc.id}"
}
```

 

```
output "default_domain" {
  value = "${alicloud_cs_application.wordpress.default_domain}"
}
```

**variables.tf**

该文件包含可传递到 main.tf 的变量，可帮助您自定义环境。

```
variable "alicloud_access_key" {
  description = "The Alicloud Access Key ID to launch resources. Support to environment 'ALICLOUD_ACCESS_KEY'."
}
```

```
variable "alicloud_secret_key" {
  description = "The Alicloud Access Secret Key to launch resources.  Support to environment 'ALICLOUD_SECRET_KEY'."
}
```

```
variable "region" {
  description = "The region to launch resources."
  default = "cn-hongkong"
}
```

```
variable "vpc_cidr" {
  description = "The cidr block used to launch a new vpc."
  default = "172.16.0.0/12"
}
```

```
variable "app_name" {
  description = "The app resource name. Default to variable `resource_group_name`"
  default = "wordpress"
}
```

**wordpress.yml**

部署 Wordpress 应用的 Compose 模板，该模板来自于控制台提供的编排模板，详情登录容器服务控制台，单击左侧导航栏中的**应用**，选择**创建应用** \> **使用编排模板创建** \> **使用已有编排模板**。

## 步骤 3 执行 Terraform 脚本 {#section_lhv_ty1_ydb .section}

要想运行脚本，首先定位到您存放以上文件的目录，如 /root/terraform/wordpress。您可以利用以下 terraform 的相关命令，运行脚本，构建容器集群和部署应用。更多的命令用法，参见 [Terraform Commands \(CLI\)](https://www.terraform.io/docs/commands/index.html)。

执行 `terraform init`，会初始化环境。

```
$ terraform init
  Initializing provider plugins...
  ...
   - Checking for available provider plugins on https://releases.hashicorp.com...
   - Downloading plugin for provider "alicloud" (1.7.2)...
   * provider.alicloud: version = "~> 1.7"
   Terraform has been successfully initialized!
   ...
```

执行 `terraform providers`，会列出安装的供应商。

```
terraform providers
.
└── provider.alicloud
```

在执行 `terraform plan` 之前，首先需要传递 AccessKey ID 和 AccessKey Secret 进行授权。

```
$ export ALICLOUD_ACCESS_KEY="AccessKey ID"
$ export ALICLOUD_SECRET_KEY="AccessKey Secret"
```

然后执行`terraform plan`，会创建一个执行计划，并帮助您了解将要创建或改变的资源。

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
data.alicloud_images.main: Refreshing state...
data.alicloud_instance_types.default: Refreshing state...
data.alicloud_zones.default: Refreshing state...
------------------------------------------------------------------------
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:
...
Plan: 9 to add, 0 to change, 0 to destroy.
------------------------------------------------------------------------
Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

确定资源已如您所愿地创建/更新后，运行`terraform apply` 命令，开始执行 Terraform 模块。

```
$ terraform apply
data.alicloud_instance_types.default: Refreshing state...
data.alicloud_images.main: Refreshing state...
data.alicloud_zones.default: Refreshing state...
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:
...
Plan: 9 to add, 0 to change, 0 to destroy.
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
  Enter a value: yes
alicloud_vpc.vpc: Creating...
...
Apply complete! Resources: 9 added, 0 changed, 0 destroyed.
Outputs:    ##注意
availability_zone = cn-hongkong-a
cluster_id = c95537435b********
default_domain = c95537435b********.cn-hongkong.alicontainer.com
vpc_id = vpc-2zeaudqan6uzt5lzry48a
vswitch_id = vsw-2ze2x92n9b5neor7fcjmr
```

`terraform apply` 命令执行完毕后, 会显示 `outputs.tf` 中定义的输出参数。在上面这个例子中，输出参数为 cs\_cluster 集群 ID、可用区、VPC ID 、VSwitch ID 名称和应用实例的 default\_domain。

通过运行 `terraform output` 命令，可随时查看输出值，帮助您配置 WordPress 应用。

```
terraform output
availability_zone = cn-hongkong-a
cluster_id = c95537435b********
default_domain = c95537435b********.cn-hongkong.alicontainer.com
vpc_id = vpc-2zeaudqan6uzt5lzry48a
vswitch_id = vsw-2ze2x92n9b5neor7fcjmr
```

您现在可以在容器服务控制台查看通过 terraform 创建的集群，查看集群信息、节点信息、日志信息和容器信息等信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6160_zh-CN.png)

同时，可以在应用页面查看 Wordpress 应用信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6162_zh-CN.png)

单击应用名称，然后单击**路由列表**，可查看路由地址。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6163_zh-CN.png)

## 步骤 4 访问 WordPressPress {#section_hzs_yt2_zdb .section}

1.  打开 Wordpress Compose 模板 `wordpress.yml`，找到应用域名前缀 `aliyun.routing.port_80: http://wordpress`。
2.  将域名前缀 `http://wordpress` 与应用的 `default_domain` 拼接后的值`http://wordpress.c95537435b********.cn-hongkong.alicontainer.com` 输入浏览器，即可访问 WordPress 欢迎页面，可选择语言，然后继续配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6164_zh-CN.png)

3.  输入站点名称以及管理员的用户名和密码。选择安装 WordPress。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6165_zh-CN.png)

4.  WordPress 安装完成后，单击**登录**，输入管理员的用户名和密码，进入 WordPress 应用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6168_zh-CN.png)


## 延伸阅读 {#section_x3v_ty1_ydb .section}

阿里云目前是 Terraform 官方的 Major Cloud Provider，如果您想通过 terraform 灵活构建阿里云上的基础设施资源，您可参见 [Alicloud Provider](https://www.terraform.io/docs/providers/alicloud/index.html) 了解更多信息，自定义资源描述文件，快速搭建属于您的云上设施 。

