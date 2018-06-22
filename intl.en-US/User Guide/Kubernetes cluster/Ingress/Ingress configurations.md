# Ingress configurations {#concept_rmj_1yn_vdb .concept}

Alibaba Cloud Container Service provides the highly reliable Ingress controller components and integrates with Alibaba Cloud Server Load Balancer to provide the flexible and reliable Ingress service for your Kubernetes clusters.

See the following Ingress orchestration example. You must configure the annotation when creating an Ingress in the Container Service console. Some configurations must create dependencies. For more information, see [Create an Ingress in Container Service console](reseller.en-US/User Guide/Kubernetes cluster/Ingress/Create an Ingress in Container Service console.md#), [Support for Ingress](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#), and [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/).

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: / ##The annotation.
  name: nginx-ingress ##The Ingress name.
spec:
  rules: ##The Ingress rule.
  - host: *.xxxxxxx.cn-hangzhou.alicontainer.com ##The default service access domain name of the cluster.
    http:
      paths:
      - backend:
          serviceName: http-svc1 ##The backend service.
          servicePort: 80                                        
        path: /svc1 ## The URL sub-domain.
  - host: foo.bar.com ##The testing domain name.
    http:
      paths:
      - backend:
          serviceName: http-svc2
          servicePort: 80
        path: /svc2
      - backend:
          serviceName: http-svc3
          servicePort: 80
        path: /svc3
  tls: ## Enable TLS to configure the secure Ingress service.
  - hosts:
    - *.xxxxxx.cn-hangzhou.alicontainer.com
    - foo.bar.com
    secretName: nginx-ingress-secret ##The name of the used secret.
```

## Annotations {#section_ojg_fyn_vdb .section}

You can configure the Ingress annotations, the used Ingress controller, and the Ingress rules. For more information about the common rewrite annotations, see [rewrite annotations](https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/rewrite/README.md).

For example, a typical rewrite annotation `nginx.ingress.kubernetes.io/rewrite-target: /` redirects the path /path to the path / that can be recognized by the backend services.

## Rules {#section_pjg_fyn_vdb .section}

The rules indicate those that authorize the inbound access to the cluster and are generally the HTTP rules, including the domain name \(virtual hostname\), URL access path, service name, and port.

You must complete the following configurations for each HTTP rule:

-   Domain: Enter the testing domain name of an Alibaba Cloud Kubernetes cluster or a virtual hostname, such as `foo.bar.com`.
-   Path: Specify the URL path of the service access. Each path is associated with a backend service. Before Alibaba Cloud Server Load Balancer forwards the traffic to the backend, all inbound requests must match with the domain name and path.
-   Name and Port: The backend configuration, which is a combination of `service:port`, for example, `http-svc1:80`. The Ingress traffic is forwarded to the matched backend services.

## TLS {#section_rjg_fyn_vdb .section}

You can encrypt the Ingress by specifying a secret that contains the TLS private key and certificate to implement the secure Ingress access. The TLS secret must contain the certificate named tls.crt and private key named tls.key. For more information about the TLS principles, see [TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls). For how to create a secret, see [Configure a safe routing service](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#section_j4d_jrs_vdb).

## Tags {#section_sjg_fyn_vdb .section}

You can add tags for Ingress to indicate the characteristics of the Ingress.

