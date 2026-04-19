---
title: "acme使用方法"
date: 2025-04-02T14:07:49+08:00
description: "acme使用方法"
showToc: false
categories:
  - "教程"
tags:
  - "acme"
cover:
    image: "https://raw.githubusercontent.com/ffxdpie/blog/main/fx/image-20250525225810163.png"
    alt: "acme使用方法"
    relative: false
---

[acme.sh Github地址](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

![image-20250525225810163](https://raw.githubusercontent.com/ffxdpie/blog/main/fx/image-20250525225810163.png)

###### 1.申请证书

```shell
acme.sh --issue --dns dns_cf -d '*.uiuuyr.top'
```

###### 2.安装证书

```shell
acme.sh --install-cert -d *.uiuuyr.top \
--key-file       /etc/nginx/*.uiuuyr.top/key.pem  \
--fullchain-file /etc/nginx/*.uiuuyr.top/cert.pem \
--reloadcmd     "service nginx reload"
```

