---
title: "conda 基本使用方法"
date: 2023-07-26T16:45:58+08:00
description: "conda 常用命令"
categories:
  - "教程"
tags:
  - "conda"
  - "python"
# 默认不显示目录，如需开启请添加 showToc: true
---

## conda 常用命令

### 查看安装了那些包
```shell
conda list
```


###  看当前存在哪些虚拟环境
```shell
conda env list conda info -e
```


 ###  检查更新当前的conda
```shell
conda update conda
```


### Python创建虚拟环境
```shell
conda create -n your_env_name python=x.x 
```

- anaconda命令创建python版本为x.x，名字为your_env_name的虚拟环境。your_env_name文件可以在Anaconda安装目录envs文件下找到。
### 激活或者切换虚拟环境

 打开命令行，输入python --version检查当前 python 版本。
- Linux:  

  ```shell
  source activate your_env_nam
  ```
- Windows: 

  ```shell
  activate your_env_name
  ```
###  对虚拟环境中安装额外的包
```shell
conda install -n your_env_name [package]
```


### 关闭虚拟环境(即从当前环境退出返回使用PATH环境中的默认python版本)
```shell
deactivate env_name
```


- 或者`activate root`切回root环境
- Linux下：

  ```shell
  source deactivate 
  ```

  
###  删除虚拟环境
```shell
conda remove -n your_env_name --all
```


### 删除环境钟的某个包
```shell
conda remove --name $your_env_name  $package_name 
```


### 设置国内镜像

- http://Anaconda.org 的服务器在国外，安装多个packages时，conda下载的速度经常很慢。清华TUNA镜像源有Anaconda仓库的镜像，将其加入conda的配置即可添加Anaconda的TUNA镜像
- 添加Anaconda的TUNA镜像,清华镜像源

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn
```

​    
- 设置搜索时显示通道地址

```shell
conda config --set show_channel_urls yes
```


###  恢复默认镜像

```shell
conda config --remove-key channels
```

