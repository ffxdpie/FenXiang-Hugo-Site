---
title: "解决一个Windows下启动项重复的问题"
date: 2023-12-16T00:59:58+08:00
description: "解决一个Windows下启动项重复的问题"
# 明确关闭目录显示
showToc: false
categories:
  - "问题处理"
tags:
  - "Windows常见问题"
---

# 解决一个Windows下启动项重复的问题

​    我喜欢Windows在运行的时候，任务管理器出现在右下的系统托盘里，便于监视系统的运行情况。为了能够开机自动运行任务管理器，我在”开始菜单”-->“程序”-->“启动”项中增加了指向任务管理器(taskmgr.exe)的快捷方式。这时问题出现了，当我开机的时候，taskmgr.exe会运行两次。

​    运行Windows自动的工具msconfig，发现startup栏中有两条taskmgr记录：

```shell
          taskmgr   C:\WINDOWS\System32\taskmgr.exe   Common Startup
          taskmgr   C:\WINDOWS\System32\taskmgr.exe   Startup
```

​    为什么会出现Common Startup和Startup这两条记录呢？通过搜索互联网，我找到 [参考资料一](https://mail.google.com/mail/u/0/#label/Study%2FComputer/131f97e61fa83fc0)。这篇文件详细介绍了msconfig中的这些startup记录是如何来的，自然也讲到了Common Startup和Startup的由来：（假设我登录Windows的用户名是tom）

​    1. Common Startup对应C:\Documents and Settings\ All Users\Start Menu\Programs\Startup目录下的项；

​    2. Startup对应C:\Documents and Settings\tom\Start Menu\Programs\Startup目录下的项。

​    文章还给出了一种验证的方法：右击“开始菜单”-->“程序”-->“启动”，在弹出的上下文菜单中，单击最上面的“打开”项，出来的目录是对应Startup项的；如果单击第二项“打开所有用户”，出来的目录是对应Common Startup项的。奇怪的是，我按照这种验证方法，出来目录的全都是指向All Users，没有指向tom这个用户的，这也是taskmgr.exe会运行两次的根本原因。

​    继续上网查找，找到了 [参考资料二](http://topic.csdn.net/u/20101219/08/3068cb0f-4fae-4922-9bcf-cdeebf537037.html)，里面的大牛指出了上述行为是受注册表控制的。我打开我的注册表，看到了下面两条：

```shell
        [HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\ Shell Folders]
                Start Menu = C:\Documents and Settings\All Users\Start Menu
                Startup = C:\Documents and Settings\All Users\Start Menu\Programs\Startup
        [HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\ User Shell Folders]
                Start Menu = %ALLUSERSPROFILE%\Start Menu
                Startup = %ALLUSERSPROFILE%\Start Menu\Programs\Startup
```

​    很明显，第一条Shell Folders对应的是Common Startup，第二User Shell Folders条对应的是Startup。因为%ALLUSERSPROFILE%等于C:\Documents and Settings\All Users，所以我系统里的Common Startup和Startup指向了同一目录。



​    到此为止，终于找到真凶了，原来是注册表乱了。解决办法很简单，将注册表指向正确的位置就可以了。

​    参考资料：
​        一、https://mail.google.com/mail/u/0/#label/Study%2FComputer/131f97e61fa83fc0
​       二、http://topic.csdn.net/u/20101219/08/3068cb0f-4fae-4922-9bcf-cdeebf537037.html
