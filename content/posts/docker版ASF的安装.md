---
title: "docker版ASF的安装"
date: 2023-07-24T22:43:02+08:00
description: "docker版ASF的安装"
showToc: true
TocOpen: false
categories:
  - "教程"
tags:
  - "ASF"
  - "docker"
---

# 安装ASF

```shell
mkdir -p /root/ASF #新建可以任意文件夹

docker run -p 1242:1242 -v /root/ASF/config:/app/config -v /root/ASF/plugins:/app/plugins --name ASF --pull always justarchi/archisteamfarm:latest

```



## 配置文件

此示例假定您将使用单个 ASF 容器，所有配置文件都放在 `/opt/docker/ASF/config`。 您需要修改此处的配置文件路径以匹配您的环境。 如果您打算编写内容如下的 `IPC.config` 配置文件，则此 ASF 也能够正常启用 IPC 接口：

```shell
{
    "Kestrel": {
        "Endpoints": {
            "HTTP": {
                "Url": "http://*:1242"
            }
        }
    }
}
```

## ASF.json

```shell
{
 "AutoRestart": true,
 "CommandPrefix": "!",
 "ConfirmationsLimiterDelay": 10,
 "ConnectionTimeout": 60,
 "CurrentCulture": "zh-CN",
 "Debug": false,
 "FarmingDelay": 15,
 "GiftsLimiterDelay": 1,
 "Headless": false,
 "IdleFarmingPeriod": 8,
 "InventoryLimiterDelay": 3,
 "IPC": true,
 "IPCPassword": "password",
 "IPCPrefixes": [
  "http://*:1242/"
 ],
 "LoginLimiterDelay": 10,
 "MaxFarmingTime": 10,
 "MaxTradeHoldDuration": 15,
 "OptimizationMode": 0,
 "Statistics": true,
 "SteamMessagePrefix": "/me ",
 "SteamOwnerID": 0,
 "SteamProtocols": 7,
 "UpdateChannel": 1,
 "UpdatePeriod": 24,
 "WebLimiterDelay": 200,
 "WebProxy": null,
 "WebProxyPassword": null,
 "WebProxyUsername": null,
 "Blacklist": [
  730,
  440,
  221100,
  10,
  240,
  50,
  70,
  500,
  550,
  20,
  30,
  300,
  320,
  40,
  60,
  80,
  360,
  58610,
  222880,
  4000,
  252490,
  2100,
  61700,
  21970,
  1250,
  204300,
  219640,
  10180,
  115300,
  42700,
  2400,
  216250,
  255220,
  65800,
  4920,
  35450,
  44350,
  221040,
  70000,
  282800,
  227100,
  215470,
  222480,
  208090,
  243800,
  17710,
  1200,
  63000,
  63500,
  61730,
  45500,
  63200,
  39000,
  224260,
  223710,
  104900,
  55110,
  247730,
  55100,
  6510,
  214360,
  214630,
  63970,
  42160,
  204080,
  212410,
  209610,
  201070,
  244630,
  91310
 ]
}
```

简单介绍几个重要的配置：

- **“Blacklist”**：不挂卡的游戏 (这里排除了所有带 **VAC** 的游戏)
- **“SteamOwnerID”**：**ASF** 所有者的 `SteamID`；**ASF** 所有者可以使用全局指令 (例如：`!exit`、`!restart`、`!update` 等 )
  获取 `SteamID` 的方法：[怎么获取 SteamID？](https://zhuanlan.zhihu.com/p/28256575)

详细的配置说明请参考：[ASF.json 详细配置说明](https://mogeko.me/posts/zh-cn/036/#asf-json-详细配置说明)
