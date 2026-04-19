---
title: "构建fastapi docker"
date: 2025-03-23T16:45:00+08:00
description: "docker的使用方法"
categories:
  - "教程"
tags:
  - "docker"
# 默认不显示目录，如需开启请添加 showToc: true
---



进入文件路径   /home/fenxiang/nsgkapi

就修改后的文件上传

构建docker 命令

```shell
docker build -t kulipa/nsgkapi .
```

运行docker

```shell
docker run -d --network api --name nsgkapi kulipa/nsgkapi 
```

登录docker

```shell
docker login
```

推送到dockerhub命令

```shell
docker push Kulipa/nsgkapi:latest
```

拉取命令

```shell
docker pull kulipa/nsgkapi:latest
```

