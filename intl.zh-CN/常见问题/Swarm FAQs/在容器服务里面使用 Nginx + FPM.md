# 在容器服务里面使用 Nginx + FPM {#concept_utc_kys_vdb .concept}

如果您需要在容器服务里面使用 Nginx + FPM，推荐您使用镜像 [https://github.com/ngineered/nginx-php-fpm](https://github.com/ngineered/nginx-php-fpm) 作为基础镜像，将 Nginx 和 FPM 放在一个镜像里。

该镜像可以用于创建一个 Nginx + PHP-FPM 容器。当容器创建后，可以实现从 Git 中拉取网站代码，同时容器也可以将代码变更推送到 Git 或者从 Git 上拉取代码变更。此外，容器还可以使用传递给 Docker 的变量更新编排文件，从而更新您的代码和设置。

该镜像还支持 Let’s Encrypt SSL 配置、自定义 Nginx 配置、修改 Nginx/PHP 的配置、X-Forwarded-For 头和 UID 映射（支持本地数据卷）。

