---
title: "双开steam及其游戏"
date: 2023-07-30T23:23:01+08:00
description: "双开steam"
categories:
  - "教程"
tags:
  - "steam"
# 默认不显示目录，如需开启可手动添加 showToc: true
---

## 教程如下：

1. 关闭蒸汽

2. 以管理员身份打开cmd.exe（不是100%确定是否需要管理员）

3. 设置变量，whatever 可以是任何值

   ```shell
   set VPROJECT=whatever
   ```

4. CD 进入steam目录

   ```shell
   cd "C:\Program Files (x86)\Steam"
   ```

5. 启动第一个窗口

   ```shell
   steam.exe -master_ipc_name_override test1
   ```

6. 启动第二个窗口

   ```shell
   steam.exe -master_ipc_name_override test2
   ```

7. 打开两个 Steam 客户端，每个客户端登录不同的帐户

8. 在两个steam上添加

   ```shell
   -allowmultiple
   ```

   到CSGO启动选项

9. 在两个实例上从 Steam 库中打开 csgo

10. 玩得愉快



这样做的原因是 Steam 使用共享内存和事件来建立从游戏到 Steam 客户端的初始通信。默认情况下，共享内存和事件的名称为

```shell
Steam3Master_SharedMemFile
Steam3Master_SharedMemLock
```

但您可以通过设置环境变量用任何值覆盖 Steam3Master 部分。Steam 启动选项 -master_ipc_name_override 将使用自定义前缀创建这些对象，并为打开的任何游戏设置所需的环境变量，以及使用环境变量。

仅仅这样仍然不能打开csgo，因为csgo会创建一个互斥锁，并且默认情况下不允许运行两个实例。这就是 -allowmultiple csgo 启动选项的用武之地。但是有一个警告，除非设置 VPROJECT env 变量，否则它将无法工作，所以必须在打开 Steam 之前设置它，以便 csgo 也将继承它，任何值都可以。
