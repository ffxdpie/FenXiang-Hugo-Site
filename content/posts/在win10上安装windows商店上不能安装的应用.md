---
title: "在win10上安装windows商店上不能安装的应用"
date: 2025-04-30T20:09:12+08:00
description: "记录安装apple music"
categories:
  - "教程"
tags:
  - "Windows常见问题"
cover:
    image: "https://raw.githubusercontent.com/ffxdpie/blog/main/fx/查看版本号.png"
    alt: "在win10上安装windows商店上不能安装的应用"
    relative: false
---

# 关于在win10上安装win11商店apple music体验版的方法

## 一、下包

在网页端微软商店搜索所需的软件，并进入软件详情页，本次的体验版软件地址如下：

[https://apps.microsoft.com/store/detail/apple-music-preview/9PFHDD62MXS1?hl=en-us&amp;gl=us](https://apps.microsoft.com/store/detail/apple-music-preview/9PFHDD62MXS1?hl=en-us&amp;gl=us)


复制该地址，后打开 [https://store.rg-adguard.net/](https://store.rg-adguard.net/ ) ，在搜索栏贴上，右侧勾选slow后回车


找到标题中有所需软件名称，且后缀为msixbundle的选项，点击下载

## 二、解包

下载完成后，将该文件的后缀改为 zip \ 7z \ rar 等压缩文件的后缀解压并打开


打开后继续找到名称中含有“x64”的安装包，更改后缀为压缩包格式后，解压到其他文件夹并打开

## 三、改包

找到 AppManifest.xml ，用记事本打开

回到软件详情页，找到软件系统要求，

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/查看版本号.png" alt="查看版本号" />

复制要求的系统版号并在记事本中搜索（ctrl+F）

更改10.0.*****.0中间那串就行

<img src="https://raw.githubusercontent.com/ffxdpie/blog/main/fx/修改版本号.png" alt="修改版本号" />

在设置中找到自己电脑的系统版号，将刚刚搜索的软件要求系统版号修改为自己的系统版号


保存并退出

## 四、重封

借文章开头的参考文章中的链接下载WSAppBak，然后解压到任意目录，打开  [ WSAppBak.exe](https://github.com/Wapitiii/WSAppBak)

先复制上一步解压文件的所在地址，在 [ WSAppBak.exe](https://github.com/Wapitiii/WSAppBak) 贴上，回车

后在其他路径新建一个文件夹，复制该文件夹地址，贴上，回车

等待流程走完，期间会有数个弹窗，直接点OK、是

完成后 WSAppBak.exe 的进程会显示完成，点击任意键自动关闭

## 五、签名

在上一步新建的文件夹中，找到后缀.cer的文件，点击打开


点击安装证书——本地计算机——将所有证书都放入下列存储——受信任的根证书颁发机构

（此步与先前提到的参考文章略有出入，如安装失败可跟着参考文章再来一次）

## 六、安装

恭喜你，完成以上所有步骤后，你应该能完成安装啦！

点击后缀为.appx的文件，即可进行安装。



