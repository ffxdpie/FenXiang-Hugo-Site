---
title: "n1安装armbian以及安装docker、openwrt"
date: 2023-07-24T12:45:58+08:00
description: "n1安装armbian后安装docker"
showToc: true
TocOpen: false
categories:
  - "教程"
tags:
  - "armbian"
  - "N1盒子"
---

参考教程 ：[[N1盒子\]](https://www.right.com.cn/forum/forum.php?mod=forumdisplay&fid=158&filter=typeid&typeid=21) **N1刷Armbian系统并在Docker中安装OpenWrt旁路由的详细教程**

## 刷入Armbian

Armbian（下载地址）：[Armbian](https://github.com/ophub/amlogic-s9xxx-armbian)

输入官方写入EMMC命令：

```shell
armbian-install
```

## 安装及升级 Armbian 的相关说明

中文说明：[https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/README.cn.md](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/README.cn.md)

### 更新 Armbian 内核

登录 Armbian 系统 → 输入命令：

```shell
# 使用 root 用户运行 (sudo -i)
# 如果不指定参数，将更新为最新版本。
armbian-update
```

### 安装常用软件

登录 Armbian 系统 → 输入命令：

```shell
armbian-software
```

使用 `armbian-software -u` 命令可以更新本地的软件中心列表。根据用户在 [Issue](https://github.com/ophub/amlogic-s9xxx-armbian/issues) 中的需求反馈，逐步整合常用[软件](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/armbian-files/common-files/usr/share/ophub/armbian-software/software-list.conf)，实现一键安装/更新/卸载等快捷操作。包括 `docker 镜像`、`桌面软件`、`应用服务` 等。详见更多[说明](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/documents/armbian_software.md)。

### 修改 Armbian 配置

登录 Armbian 系统 → 输入命令：

```shell
armbian-config
```

### 为 Armbian 创建 swap

如果你在使用 `docker` 等内存占用较大的应用时，觉得当前盒子的内存不够使用，可以创建 `swap` 虚拟内存分区，将磁盘空间的一定容量虚拟成内存来使用。下面命令输入参数的单位是 `GB`，默认为 `1`。

登录 Armbian 系统 → 输入命令：

```shell
armbian-swap 1
```

### 备份/还原 EMMC 原系统

支持在 `TF/SD/USB` 中对盒子的 `EMMC` 分区进行备份/恢复。建议您在全新的盒子里安装 Armbian 系统前，先对当前盒子自带的安卓 TV 系统进行备份，以便日后在恢复电视系统等情况下使用。

请从 `TF/SD/USB` 启动 Armbian 系统 → 输入命令：

```shell
armbian-ddbr
```

详情访问github

### 清华源 /etc/apt/sources.list

```yaml
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
```

## 安装docker

apt install docker.io

### 安装docker管理面板

```shell
docker volume create portainer_data
```

```shell
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer:linux-arm64
```

### 安装docker版openwrt

[F大教程](https://www.right.com.cn/forum/thread-958173-1-1.html)

开启网卡的混杂模式（这样是为了目前这个阶段能正常安装并配置，要永久开启需修改配置文件，稍后详述）

```shell
ip link set eth0 promisc on
```

创建docker的虚拟网络（可用docker network ls查看已创建了哪些）

```shell
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 macnet
```

拉取镜像

```shell
docker pull unifreq/openwrt-aarch64:latest
docker run --name=openwrt --restart always -d --network macnet --privileged unifreq/openwrt-aarch64:latest /sbin/init
```

### 其他事项

如此设置后，会存在如下问题：

同网段设备均可以正常访问（包括ping）N1和op，但N1和op之间是不能互访的【需要将armbian的ip设置为静态的才行】
```shell
vim /etc/config/network
```


设置openwrt的静态地址

N1重启网卡混杂模式会失效，需设为永久开启

解决方案是修改/etc/network/interfaces文件：（谨慎操作，可能导致armbian永远无网络）


```shell
auto eth0
iface eth0 inet manual
up ip link set eth0 promisc on
auto macvlan
iface macvlan inet static
  address 192.168.12.104→修改成你需要的
  netmask 255.255.255.0
  gateway 192.168.12.1→需改成你的主路由ip或192.168.12.5（旁路由的ip）
  dns-nameservers 192.168.12.1→需改成你的主路由ip或12.5或再加1个公共dns比如114
  pre-up ip link add macvlan link eth0 type macvlan mode bridge
  post-down ip link del macvlan link eth0 type macvlan mode bridge
```

没有网的原因是没有添加dns服务

## 安装docker版clash

Clash在Docker中的部署

部署可视化clash dashboard容器

首先将dashboard的image pull下来。

```shell
docker pull haishanh/yacd
```

#### 然后直接跑起来就行，注意映射一个没在使用的端口就行

```shell
docker run -p 1234:80 -d haishanh/yacd
```

#### 在浏览器键入服务器地址跟端口号，我这里是输入192.168.199.10:1234，就可以打开dashboard页面



## 部署clash容器

准备好你的配置文件config.yaml，节点和订阅得自己想办法!

将下边的代码覆盖入你原有的基础配置

```yaml
port: 7890
socks-port: 7891
#转发端口一定要配置
redir-port: 7892
#允许接管局域网流量
allow-lan: true
#默认代理模式
mode: Rule
log-level: warning
#接口控制端口你可以自己设置，默认是9090
external-controller: 0.0.0.0:7070
#如果服务器对公网开放可以设置密码
secret: ""
#external-ui: dashboard
#配置由clash接管的dns解析
dns:
  enable: true
#主要监听定向转发来的数据，后续会在路由表里配置转发端口为1053
  listen: 0.0.0.0:1053
  enhanced-mode: redir-host
#见下方链接
  nameserver:
    - '114.114.114.114'
    - '223.5.5.5'
  fallback:
    - 'tls://1.1.1.1:853'
    - 'tcp://1.1.1.1:53'
    - 'tcp://208.67.222.222:443'
    - 'tls://dns.google'
```
准备好配置文件之后 ，运行如下命令启动clash容器
```shell
docker run --name Clash -d -v /opt/docker/clash/config.yaml:/root/.config/clash/config.yaml --network macnet --ip 192.168.1.100 --cap-add=NET_ADMIN --privileged dreamacro/clash
```

