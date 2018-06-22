# scale {#concept_ac2_zsr_xdb .concept}

设置该服务的容器数量，横向扩展服务。

目前，Docker Compose 只能在每一个服务中启动一个容器，如果需要扩展容器数量，需要在启动后手动进行设置。

现在通过`scale`的扩展标签，支持您在容器启动的时候进行扩展。

此外，在容器被删除之后，您可以在容器服务管理控制台对应用进行重新部署（单击左侧导航栏中的**应用**，选择目标应用并单击右侧的**重新部署**），容器服务会重启或新建容器使容器恢复到指定数量。

**示例：**

```
web:
  image: wordpress:4.2
  ports:
    - 80
  links:
    - db:mysql
  labels:
    aliyun.scale: "3"
db:
  image: mysql
  environment:
    - MYSQL_ROOT_PASSWORD=password
```

