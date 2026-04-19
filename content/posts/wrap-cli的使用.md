---
title: "wrap-cli的使用"
date: 2023-07-08T14:16:33+08:00
description: "利用 Cloudflare WARP 官方客户端提供 socks5 代理，解决 VPS 只有纯 IPv6 或 IP 归属地（送中）等问题。"
showToc: true
TocOpen: false
categories:
  - "教程"
tags:
  - "wrap"
---
## 前言

现在纯 IPv6、nat IPv4 VPS 越来越多了，于是有人发现可以用 warp 解锁 Netflix、解决 Google 送中问题，但是大多数教程和一键脚本都是用 [wgcf](https://github.com/ViRb3/wgcf) 来实现的。其实 wgcf 有相对不低的延迟，大部分情况下使用这种方案会造成打开网页缓慢的问题。因此我们可以利用 warp 官方客户端来提供 socks5 给别的软件分流使用。

## 安装

以 `Debian 11` 为例：

首先，安装存储库的 GPG 密钥：

```shell
apt install sudo gpg
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
```

然后添加存储库：

```shell
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ bullseye main' | sudo tee /etc/apt/sources.list.d/cloudflare-client.list
```

更新 APT 缓存：

```首shell页
sudo apt update
```

安装 `Cloudflare WARP`

```shell
sudo apt install cloudflare-warp
```

## 使用

注册一个 warp 账号：

```shell
warp-cli register
```

如果想要使用已经有的账号则可以指定 `license` (1.1.1.1 app 右上角 - 账户 - 按键），可以通过邀请新用户的方式为账号添加 warp + 高级流量，也可以通过脚本刷流量，[点击前往教程](https://yushum.com/archives/580)。

```shell
warp-cli set-license <key>  //将<key>替换为你的license
```

修改 warp-cli 运行模式：

```shell
warp-cli set-mode proxy
```

设置监听端口：

```shell
warp-cli set-proxy-port 10086
```

连接：

```shell
warp-cli connect
```

查看当前 warp 的 IP：

```shell
curl -4 ip.gs -x socks5://127.0.0.1:10086
```

然后我们就可以将其他软件需要分流的流量转发到 10086 端口了。

以 `Xray/V2Ray` 为例：

在配置文件中的添加 `outbounds`：

```shell
{
      "protocol": "socks",
      "settings": {
         "servers": [{
            "address": "127.0.0.1",
            "port": 10086
         }]
      },
      "tag": "warp"
}
```

在路由 `routing` 中加入：

```shell
{
	"type": "field",
	"outboundTag": "warp",
	"domain": [
		"geosite:netflix"
	]
}
```

然后重启即可：

```shell
systemctl restart xray.service
```

测试无误之后便可以设置 warp-cli 长期运行：

```shell
warp-cli enable-always-on
```

## 结语

这种方案相较于目前流行的 wireguard 方案的优势就是可以只分流需要分流的流量，其他无论什么流量都不会受到影响。

另外 wireguard 的方案会造成 docker 的 bridge 模式无法使用，这种方案可以完美解决。

## 参考链接

1. [Announcing WARP for Linux and Proxy Mode](https://blog.cloudflare.com/announcing-warp-for-linux-and-proxy-mode/)
2. [Cloudflare Package Repository](https://pkg.cloudflareclient.com/install)

<iframe height="1" width="1" style="box-sizing: border-box; border: none; color: rgb(82, 95, 127); font-family: &quot;Noto Serif SC&quot;, serif, system-ui; font-size: 17px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: left; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial; top: 0px; left: 0px; visibility: hidden;"></iframe>

## xray的配置文件

```yaml
{
    "outbounds":[
        {
            "protocol":"freedom"
        },
        {
            "tag":"warp",
            "protocol":"socks",
            "settings":{
                "servers":[
                    {
                        "address":"127.0.0.1",
                        "port":40000
                    }
                ]
            }
        },
        {
            "tag":"WARP-socks5-v4",
            "protocol":"freedom",
            "settings":{
                "domainStrategy":"UseIPv4"
            },
            "proxySettings":{
                "tag":"warp"
            }
        },
        {
            "tag":"WARP-socks5-v6",
            "protocol":"freedom",
            "settings":{
                "domainStrategy":"UseIPv6"
            },
            "proxySettings":{
                "tag":"warp"
            }
        }
    ],
    "routing":{
        "rules":[
            {
                "type":"field",
                "domain":[
                    "geosite:openai",
                    "ip.gs"
                ],
                "outboundTag":"WARP-socks5-v4"
            },
            {
                "type":"field",
                "domain":[
                    "geosite:google",
                    "geosite:netflix",
                    "p3terx.com"
                ],
                "outboundTag":"WARP-socks5-v6"
            }
        ]
    }
}
```

## 原配置备份

```yaml
{
  "api": {
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ],
    "tag": "api"
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 62789,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      },
      "tag": "api"
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "policy": {
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true
    }
  },
  "routing": {
    "rules": [
      {
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api",
        "type": "field"
      },
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked",
        "type": "field"
      },
      {
        "outboundTag": "blocked",
        "protocol": [
          "bittorrent"
        ],
        "type": "field"
      }
    ]
  },
  "stats": {}
}
```

