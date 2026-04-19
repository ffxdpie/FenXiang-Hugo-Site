---
title: "WinRAR深度去除弹窗广告"
date: 2023-07-09T23:37:51+08:00
description: "WinRAR 是一款不错的解压缩软件，但还是收费软件，广告不少，今天就总结了一下网上的各路教程。"
categories:
  - "教程"
tags:
  - "WinRAR"
---



## 直接使用该软件即可完成winrar的安装和注册，以下内容可以不看！！！！！

[WinRAR-Extractor](https://github.com/lvtx/WinRAR-Extractor)



## WinRAR 是一款不错的解压缩软件，但还是收费软件，广告不少，今天就总结了一下网上的各路教程

[本教程搬运自吾爱破解论坛](https://www.52pojie.cn/forum.php?mod=viewthread&tid=1143884&highlight=rar)

首先通过特殊方式获取软件许可：
新建一个文本文档

在这个文本文档里输入内容：

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/密钥.png" alt="密钥" />

纯文本查看

```shell
RAR registration data
Federal Agency for Education
1000000 PC usage license
UID=b621cca9a84bc5deffbf
6412612250ffbf533df6db2dfe8ccc3aae5362c06d54762105357d
5e3b1489e751c76bf6e0640001014be50a52303fed29664b074145
7e567d04159ad8defc3fb6edf32831fd1966f72c21c0c53c02fbbb
2f91cfca671d9c482b11b8ac3281cb21378e85606494da349941fa
e9ee328f12dc73e90b6356b921fbfb8522d6562a6a4b97e8ef6c9f
fb866be1e3826b5aa126a4d2bfe9336ad63003fc0e71c307fc2c60
64416495d4c55a0cc82d402110498da970812063934815d81470829275
```

然后将文件名改为：**rar**reg.key

<img src="214226p1hhh4syyssy0ocy.png" alt="" />

再将这个文件导入WinRAR的安装文件夹

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/214322q3iy77nlx3bmy5lc.png" alt="" />

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/214418hzbk2k2i0qjtboxg.png" alt="" />

这时点开关于WinRAR，已经获取许可。

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/214523wozu2ik8ry808o88.png" alt="" />

接下来使用Resource Hacker软件打开winrar.exe



<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/214704hbjodro9drbf062r.png" alt="" />

进入字串表，找到“80”，删除“1267”和“1277”行
点击绿色三角形按钮，编译。

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/215135ubka54huovatfojh.png" alt="" />

然后：文件→另存为，进行保存。

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/220229qbsuhlbw4el40hs0.jpg" alt="" />

然后对源文件：winrar.exe进行替换,注意，要关闭winrar软件

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/220929hpwlol99o99apph3.png" />
