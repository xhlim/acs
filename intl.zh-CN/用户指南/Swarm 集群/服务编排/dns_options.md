# dns\_options {#concept_fzp_mwr_xdb .concept}

设置 DNS 选项，和`docker run` 命令中的--dns-opt参数语义一致。

```
wordpress:
  image: wordpress:4.2
  dns_options: 
    - "use-vc"
```

