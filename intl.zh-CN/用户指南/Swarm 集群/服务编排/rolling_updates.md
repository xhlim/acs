# rolling\_updates {#concept_zqq_zrr_xdb .concept}

更新某个服务时，如果该服务包括超过一个以上容器（使用 `scale` 标签定义），在第 n 个容器更新成功后，再去做第 n+1 个容器的更新，以此来最小化停止服务时间。

**示例：**

部署 WordPress 服务，通过`scale`标签指定部署 2 个容器，使用`rolling_updates`标签可以使 WordPress 对外停止服务的时间最小化。

```
web:
  image: wordpress
  ports:
    - 80
  restart: always
  links:
    - 'db:mysql'
  labels:
    aliyun.logs: /var/log
    aliyun.routing.port_80: http://wordpress
    aliyun.rolling_updates: 'true'
    aliyun.scale: '2'
db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: example
  restart: always
  labels:
    aliyun.logs: /var/log/mysql
```

## parallelism {#section_upx_csr_xdb .section}

您可以使用 `parallelism` 标签定义 `rolling_updates` 每次并行更新的容器数量。

**Note:** 此标签必须和 `rolling_update` 配合使用，单独使用无效。

**取值：**

-   默认值为 1，即每次只更新一个容器。
-   当其值大于 1 的时候，`rolling_updates` 过程中，每次会以 `parallelism` 定义的值来并行更新相应个数的容器，实现批量更新。
-   当定义值无效时，默认为 1。

    **Note:** 为了确保始终有容器在提供服务，建议 `parallelism` 定义的值小于服务包含的容器数。


**示例：**

下面的示例部署 Nginx 服务，通过 `scale` 标签部署 4 个容器，使用 `rolling_updates` 和 `parallelism` 标签定义每次以 2 个容器为单位来进行批量更新。

```
web:
  image: nginx:latest
  restart: always
  environment:
      - "reschedule:on-node-failure"
  ports:
      - 80
  labels:
    aliyun.scale: "4"
    aliyun.rolling_updates: 'true'
    aliyun.rolling_updates.parallelism: "2"
```

