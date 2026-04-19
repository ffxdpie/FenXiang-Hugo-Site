---
title: "git的使用"
date: 2023-07-24T16:45:00+08:00
description: "git的使用方法"
categories:
  - "教程"
tags:
  - "git的使用"
# 默认不显示目录，如需开启请添加 showToc: true
---



## git全局设置、查看、取消代理

下面两种二选一就可以了

### 设置socks5代理

```shell
git config --global http.proxy 'socks5://127.0.0.1:10808' 
git config --global https.proxy 'socks5://127.0.0.1:10808'
```
### 设置github.com代理

```shell
http.https://github.com.proxy=socks5://127.0.0.1:8080
https.https://github.com.proxy=socks5://127.0.0.1:8080
```

### 设置http代理

```shell
 git config --global http.proxy 'http://127.0.0.1:10809' 
 git config --global https.proxy 'http://127.0.0.1:10809'
```

### 查看代理

 ```shell
git config --global --get http.proxy
git config --global --get https.proxy
 ```

### 取消代理：

```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 取消github.com代理

```shell
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy
```
