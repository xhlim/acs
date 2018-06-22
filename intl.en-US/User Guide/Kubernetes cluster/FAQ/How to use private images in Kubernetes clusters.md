# How to use private images in Kubernetes clusters {#concept_yvc_fxb_wdb .concept}

```
kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
```

Wherein:

-   **regsecret**: Specifies the secret key name and the name is customizable.
-   **—docker-server**: Specifies the Docker repository address.
-   **—docker-username**: Specifies the user name of the Docker repository.
-   **—docker-password**: Specifies the logon password of the Docker repository.
-   **—docker-email**: Specifies the email address \(optional\).

Add secret key parameters in the YML file.

```
containers:
- name: channel
  image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
ports:
- containerPort: 8114
imagePullSecrets:
- name: regsecret
```

Wherein:

-   `imagePullSecrets` declares that specified secret key is needed when pulling the image.
-   `regsecret` must be the same as the preceding secret key name.
-   The Docker repository name in `image` must be the same as that in `--docker-server`.

For more information, see the official documentation [Use private repository](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry).

