---
title: "OpenWrt的编译和使用"
date: 2023-10-22T13:03:35+08:00
description: "OpenWrt的编译和使用"
categories:
  - "教程"
tags:
  - "openwrt"
---

# OpenWrt的编译和使用



lede仓库源码和教程    [OpenWrt的编译和使用](https://github.com/coolsnowwolf/lede)

Flippy 的 Openwrt 打包源码 [Flippy 的 Openwrt 打包源码](https://github.com/unifreq/openwrt_packit)

passwall仓库 [kenzok8](https://github.com/kenzok8/small)

仓库 里面有armbian和openwrt以及内核 [ophub](https://github.com/ophub) 

## 注意

1. **不要用 root 用户进行编译**

2. **国内用户编译前最好准备好梯子**

3. **默认登陆IP 192.168.1.99 密码 password**

## 编译命令

1. ###### 首先装好 Linux 系统，推荐 Debian 11 或 Ubuntu LTS

2. ###### 安装编译依赖

   ```
   sudo apt update -y
   sudo apt full-upgrade -y
   sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
   libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
   mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pyelftools \
   libpython3-dev qemu-utils rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip \
   vim wget xmlto xxd zlib1g-dev python3-setuptools
   ```

3. ###### 下载源代码，更新 feeds 并选择配置

   ```
   git clone https://github.com/coolsnowwolf/lede
   cd lede
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

4. ###### 下载 dl 库，编译固件 （-j 后面是线程数，第一次编译推荐用单线程）

   ```
   make download -j8
   make V=s -j1
   ```

本套代码保证肯定可以编译成功。里面包括了 R23 所有源代码，包括 IPK 的。

你可以自由使用，但源码编译二次发布请注明我的 GitHub 仓库链接。谢谢合作！

## 二次编译：

```
cd lede
git pull
./scripts/feeds update -a
./scripts/feeds install -a
make defconfig
make download -j8
make V=s -j$(nproc)
```

如果需要重新配置：

```
rm -rf ./tmp && rm -rf .config
make menuconfig
make V=s -j$(nproc)
```

编译完成后输出路径：  bin/targets



## 固件使用说明：

默认IP： 192.168.1.1  默认密码： password
注：如果用这个固件做旁路由的话不要忘了加自定义防火墙规则（网络->防火墙->自定义规则）：

```
iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE
```

也可以尝试（有桥接存在的情况下）

```
iptables -t nat -I POSTROUTING -o br-lan -j MASQUERADE 
```

AdguardHome: 固件里不包含，可以用docker方式安装, 可以双开甚至多开，灵活性很强，升级也不依赖于固件，直接用docker命令升级。

#### small仓库的使用

一键命令

```
sed -i '$a src-git kenzo https://github.com/kenzok8/openwrt-packages' feeds.conf.default
sed -i '$a src-git small https://github.com/kenzok8/small' feeds.conf.default
git pull
./scripts/feeds update -a
./scripts/feeds install -a
make menuconfig
```

**注意**

编译新版Sing-box和hysteria，需golang版本1.20或者以上版本 ，可以用以下命令

```
pushd feeds/packages/lang
rm -rf golang && svn co https://github.com/openwrt/packages/branches/openwrt-23.05/lang/golang
popd
```

### [smartdns](https://github.com/luckyyyyy/blog/issues/57)使用教程

smartdns 部分直接 vim 编辑 /etc/config/smartdns 照抄即可，无需手动设置，配置完记得界面上点击保存应用，或者uci命令刷新配置，我里面有杭州电信的DNS服务器，不是杭州的记得自己改掉，否则可能有负面效果。

```
config smartdns
	option server_name 'smartdns'
	option port '6053'
	option tcp_server '1'
	option seconddns_tcp_server '1'
	option coredump '0'
	option seconddns_server_group 'passwall'
	option seconddns_no_speed_check '1'
	option seconddns_no_dualstack_selection '1'
	option prefetch_domain '1'
	option ipv6_server '0'
	option force_aaaa_soa '1'
	option dualstack_ip_selection '1'
	option serve_expired '1'
	option redirect 'dnsmasq-upstream'
	option rr_ttl_min '300'
	option seconddns_port '7913'
	option seconddns_enabled '1'
	option seconddns_no_rule_nameserver '1'
	option seconddns_no_rule_addr '0'
	option seconddns_no_rule_soa '0'
	option seconddns_no_rule_ipset '0'
	option cache_size '300'
	option seconddns_no_cache '1'
	option enabled '1'
	list old_redirect 'dnsmasq-upstream'
	list old_port '6053'
	list old_enabled '1'

config server
	option name 'aliyun'
	option ip '223.5.5.5'
	option port '53'
	option type 'udp'
	option blacklist_ip '0'
	option server_group 'cn'
	option enabled '1'

config server
	option name '114'
	option ip '114.114.114.114'
	option port '53'
	option type 'udp'
	option blacklist_ip '0'
	option server_group 'cn'
	option enabled '1'

config server
	option enabled '1'
	option type 'udp'
	option name '电信'
	option ip '202.101.172.35'
	option port '53'
	option server_group 'cn'
	option blacklist_ip '0'

config server
	option enabled '1'
	option type 'udp'
	option name '电信'
	option ip '202.101.172.47'
	option port '53'
	option server_group 'cn'
	option blacklist_ip '0'

config server
	option type 'udp'
	option port '53'
	option name 'DNSPod'
	option ip '119.29.29.29'
	option blacklist_ip '0'
	option server_group 'cn'
	option enabled '1'

config server
	option enabled '1'
	option name 'cloud'
	option ip '1.1.1.1'
	option port '853'
	option type 'tls'
	option server_group 'passwall'
	option blacklist_ip '0'
	option addition_arg ' -exclude-default-group'

config server
	option enabled '1'
	option type 'udp'
	option name 'CNNIC SDNS'
	option ip '1.2.4.8'
	option port '53'
	option server_group 'cn'
	option blacklist_ip '0'
```



## 如何验证？

登录路由器 使用 dig 或者 nslookup 检查下各端口的DNS以及分流情况

```
nslookup www.taobao.com 127.0.0.1:7913 返回的是节点对应淘宝最快的IP
nslookup www.taobao.com 127.0.0.1:6053 返回的是国内最快的IP
nslookup www.taobao.com 应该是国内
```



<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/dns.png" alt="dns" />

注：如果手动查询规则列表内的域名，使用端口6053，然后匹配规则，转发给7913，然后被缓存住。（国外因为跳过测速，所以多个域名是正确的）



<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/112566019_p1.jpg" alt="美女" />

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/栗子.jpg" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/1698867079_new_111573410_p0-1699716220062-2.png" alt="米娘" />

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/8EE2510132283BE410AB627FEB9A41E1.jpg" alt="风景" style="zoom:67%;" />
