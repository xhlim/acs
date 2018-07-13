# Deploy Container Service clusters by using Terraform {#concept_fpj_ty1_ydb .concept}

This document introduces how to use Terraform to deploy Alibaba Cloud Container Service cluster in the Virtual Private Cloud \(VPC\) environment and deploy a sample WordPress application in the cluster.  In this document, a solution used to build Alibaba Cloud infrastructures is provided for you to use codes to automatically create, orchestrate, and manage services in Container Service.

## Prerequisite {#section_i5h_gz1_ydb .section}

-   You must activate Alibaba Cloud Container Service.
-   You must activate Alibaba Cloud Container Service and create an AccessKey for your account. Keep your AccessKey ID and AccessKey Secret properly.

## Step 1. Install Terraform {#section_jfy_gz1_ydb .section}

**Download Terraform**

Download Terraform from the [official website](https://www.terraform.io/downloads.html). Select the corresponding version and platform. In this document, install the Terraform on Linux \(the procedure is similar to that of installing the Terraform on Mac OS X\).

1.  Under Linux, click to download the `terraform_0.11.3_linux_amd64.zip` file.
2.  Copy the .zip file to an appropriate path \(/usr/local/terraform in this example\).
3.  Extract the .zip file and then get a binary file terraform.
4.  Create the following entries in the /etc/profile directory and add the path where the binary file resides \(/usr/local/terraform in this example\) to the PATH environment variable.

    ```
    export TERRAFORM_HOME=/usr/local/terraform
    export PATH=$PATH:$TERRAFORM_HOME
    ```


**Install Alibaba Cloud Terraform package**

Before using Terraform, an initialization operation is required to load Alibaba Cloud Provider. Run the following command in the template file directory:

```
terraform init
```

After the download is successful, the corresponding plugin is downloaded to the .terraform hidden directory in the current folder. If you encounter a network timeout problem during the loading process, follow the instructions to complete the manual installation of the plugin.

-   Download the corresponding version and platform Provider from [Alibaba Cloud Terraform Provider official download address](https://releases.hashicorp.com/terraform-provider-alicloud/). In this example, the Linux type is selected.
-   Copy the downloaded file terraform-provider-alicloud\_1.9.3\_linux\_amd64.zip to the Terraform installation directory /usr/local/terraform and extract it. The current directory gets Alibaba Cloud Provider terraform-provider-alicloud\_v1.9.3\_x4.

Run the following command to test the working of Terraform. If Terraform is successfully installed, the following contents are displayed:

```
$ terraform
Usage: terraform [--version] [--help] [args]

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

## Step 2. Download Container Service Terraform scripts {#section_o1j_gt5_c2b .section}

You can download the Terraform template \([the template download address](https://github.com/terraform-providers/terraform-provider-alicloud/tree/master/examples/swarm-wordpress)\) to create the swarm cluster and deploy the WordPress application . This template file defines the resources for creating a swarm cluster and the files that deploy Wordpess on the swarm cluster to help you quickly create and deploy swarm clusters. The template contains the following files after being extracted.

**main.tf**

The main file of Terraform that defines the resources to be deployed.

-   **Region** 

    Defines the region where resources are to be created.

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
    availability_zone = "${data.alicloud_zones.default.zones. 0.id}"
    name = "${var.vswitch_name}"
    cidr_block = "${var.vswitch_cidr}"
    vpc_id = "${alicloud_vpc.vpc.id}"
    }
    ```

-   **Container Service cluster**

    ```
    resource "alicloud_cs_swarm" "cs_vpc" {
    password = "${var.password}"
    instance_type = "${data.alicloud_instance_types.main.instance_types. 0.id}"
    name = "${var.cluster_name}"
    node_number = "${var.node_number}"
    disk_category = "${var.disk_category}"
    disk_size = "${var.disk_size}"
    cidr_block = "${var.cidr_block}"
    image_id = "${data.alicloud_images.main.images. 0.id}"
    vswitch_id = "${alicloud_vswitch.main.id}"
    }
    ```

-   **WordPress application**

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

This file defines the output parameters.  Resources created as part of the execution generate these output parameters. This is similar to the output parameters specified in a Resource Orchestration Service \(ROS\) template. For example, the template deploys a swarm cluster and  Wordpress application instance. The following output parameters provide the cluster ID and the default domain name for the application.

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

This file contains the variables that can be passed to main.tf and helps you customize the environment.

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

Deploy the Compose template of the WordPress application from the orchestration templates provided in the console. Log on to the Container Service console, click **Application** in the left-side navigation pane, select **Create Application** \> **Create by template** \> **Use an existing template**.

## Step 3. Run Terraform scripts {#section_lhv_ty1_ydb .section}

To run the script, first locate the directory where you stored the preceding files, such as /root/terraform/wordpress. You can use the following terraform related commands to run scripts, build container clusters, and deploy applications. For more information, see [Terraform Commands \(CLI\)](https://www.terraform.io/docs/commands/index.html).

Run `terraform init` to initialize the environment.

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

Run the `terraform providers` command to list the installed providers. 

```
terraform providers
.
└── provider.alicloud
```

Before running `terraform plan`, you must first enter the AccessKey ID and AccessKey Secret for authorization.

```
$ export ALICLOUD_ACCESS_KEY="AccessKey ID"
$ export ALICLOUD_SECRET_KEY="AccessKey Secret"
```

Run `terraform plan` to create an execution plan and help you understand the resources that are going to be created or changed.

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

After the resources are created or updated as expected, run the `terraform apply` command to start the execution of the Terraform module.

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
Outputs: ##Note
availability_zone = cn-hongkong-a
cluster_id = c95537435b********
default_domain = c95537435b********.cn-hongkong.alicontainer.com
vpc_id = vpc-2zeaudqan6uzt5lzry48a
vswitch_id = vsw-2ze2x92n9b5neor7fcjmr
```

After running the `terraform apply` command, the output parameters requested in the `outputs.tf` are displayed. In the preceding example, the output parameters are the cs\_cluster cluster ID, available zone, VPC ID, VSwitch ID name, and the default\_domain of the application instance.

The output values can be listed at any time by running the `terraform output` command to help you configure the WordPress application.

```
terraform output
availability_zone = cn-hongkong-a
cluster_id = c95537435b********
default_domain = c95537435b********.cn-hongkong.alicontainer.com
vpc_id = vpc-2zeaudqan6uzt5lzry48a
vswitch_id = vsw-2ze2x92n9b5neor7fcjmr
```

You can view the cluster created by using Terraform in the Container Service console. View the cluster, node, container, and logs.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6160_en-US.png)

At the same time, you can view the WordPress application information on the Application page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6162_en-US.png)

Click the application name, and then click **Routes** to view the route address.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6163_en-US.png)

## Step 4. Access WordPress {#section_hzs_yt2_zdb .section}

1.  Open the Wordpress Compose template `wordpress.yml` and find the application domain prefix `aliyun.routing.port_80: http://wordpress`.
2.  The value of the domain name prefix `http://wordpress` and application `default_domain` spliced with the `http: //wordpress.c95537435b********.cn-hongkong.alicontainer.com`. Enter the browser to access the WordPress welcome page, select the language, and set other configurations.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6164_en-US.png)

3.  Enter the Site Title, username, and password of the administrator.  Click Install WordPress.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6165_en-US.png)

4.  After the installation, click **Log In**. Enter the username and password of the administrator, and then click Log In on the WordPress logon page to log on to the WordPress application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7268/6168_en-US.png)


## Further information {#section_x3v_ty1_ydb .section}

Currently, Alibaba Cloud is the official major cloud provider of Terraform. To use Terraform to flexibly build Alibaba Cloud infrastructures, see [Alibaba Cloud Provider](https://www.terraform.io/docs/providers/alicloud/index.html) for more information and customize the resource description files to quickly build your cloud infrastructures.

