---
title: "docker相关帮助"
date: 2023-09-24T15:31:55+08:00
description: "docker相关帮助"
showToc: true
TocOpen: false
categories:
  - "教程"
tags:
  - "docker"
robotsNoIndex: true
---



## 要Docker容器中运行Python程序

安装Docker： 如果您尚未安装Docker，首先需要安装Docker。可以参考Docker官方网站上的安装指南，选择适合您操作系统的版本。

创建一个Dockerfile： 在您的项目目录中创建一个名为Dockerfile的文件，该文件将包含Docker容器的配置。以下是一个简单的示例Dockerfile：

```shell
#python版本
FROM python:alpine3.18

#设置工作目录
WORKDIR /app

#复制当前目录中的所有文件到容器的工作目录
COPY . .

#安装Python依赖
RUN pip install -r requirements.txt

#运行Python程序
CMD ["python", "your_script.py"]
```

在这个示例中，我们使用了官方的Python 3.9基础镜像，设置了工作目录，并将当前目录中的所有文件复制到容器中。接下来，我们通过pip install安装了Python程序所需的依赖，并使用CMD指令运行了Python程序（假设您的Python程序文件名为your_script.py）。

构建Docker镜像： 执行以下命令在项目目录中构建Docker镜像（确保您的Dockerfile位于项目目录中）：

```shell
docker build -t <yourusername/镜像名称>:<标签> <Dockerfile路径>
```

这会创建一个Docker镜像。

运行Docker容器： 使用以下命令在Docker容器中运行Python程序：

```shell
docker run my-python-app
```

这将启动一个Docker容器，运行您的Python程序。请注意，容器将在程序执行完毕后自动停止。

这些步骤假定您已经有一个Python程序，并且可能有一个名为requirements.txt的文件来指定程序的依赖项。根据您的项目的实际情况，您可以自定义Dockerfile和容器的配置。



## 上传到Docker Hub

如果您希望将这个镜像上传到Docker Hub或其他容器仓库以供他人使用，您需要首先登录到Docker Hub（或其他仓库），然后使用 docker push 命令将镜像推送到仓库。例如：

```shell
docker login
docker push <yourusername/镜像名称>:<标签> 
```



## docker run 命令

docker run 命令用于在Docker中运行容器。以下是一些常见的 docker run 命令的选项和示例用法：

基本用法：

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
OPTIONS: 用于指定各种选项的参数，如端口映射、环境变量、卷挂载等。
IMAGE: 指定要运行的Docker镜像。
COMMAND（可选）: 指定容器内要执行的命令。如果不提供，则默认运行镜像中的默认命令。
ARG...（可选）: 传递给容器内命令的参数。
常用选项：

-d 或 --detach: 让容器在后台运行（分离模式）。
-p 或 --publish: 指定端口映射，将容器内的端口映射到主机上的端口。例如 -p 8080:80 将容器内的端口80映射到主机上的端口8080。
-e 或 --env: 设置环境变量。例如 -e MYSQL_ROOT_PASSWORD=pass 可以设置MySQL的root密码。
-v 或 --volume: 挂载卷，将主机文件或目录挂载到容器内的路径。例如 -v /host/folder:/container/folder 可以将主机上的文件夹挂载到容器内的路径。
--name: 为容器指定一个名称。
--rm: 当容器停止后自动删除容器。
-it: 使用交互式终端运行容器，通常与 -d 不一起使用。
```

## 示例用法：

运行一个简单的容器并在后台运行：

```shell
docker run -d my-image
```

运行容器并设置环境变量：

```shell
docker run -e MYSQL_ROOT_PASSWORD=my-secret-pw mysql
```

运行容器并映射端口：

```shell
docker run -p 8080:80 my-web-app
```

运行容器并挂载卷：

```shell
docker run -v /host/data:/container/data my-app
```

这些示例只是 docker run 命令的一些基本用法。您可以根据您的具体需求使用不同的选项和参数来配置容器的运行方式。运行 docker run --help 命令可以查看更多详细的帮助信息。


在运行容器时，可以使用 --restart 标志来指定容器的重启策略。以下是一些可用的重启策略选项：

```shell
--restart=always：容器将始终在退出时自动重启。
--restart=on-failure：容器只在非正常退出（退出代码非零）时重启。
--restart=unless-stopped：容器将始终在退出时自动重启，除非您明确停止它。
```

例如，要创建一个一直运行并在退出时自动重启的容器，可以运行以下命令：

```shell
docker run --name my-container --restart=always my-image
```

这将创建一个名为 "my-container" 的容器，使用 "my-image" 镜像，并指定容器在退出时始终自动重启。

请注意，--restart 选项是用于 docker run 命令的一部分，而不是Docker容器本身的配置。这意味着每次运行容器时都需要指定该选项。

如果您已经运行了一个容器，并希望为其添加重启策略，您可以使用 docker update 命令来更新容器的配置，如下所示：

```shell
docker update --restart=always my-container
```

这将为名为 "my-container" 的容器添加一个始终自动重启的策略。


要让容器在后台运行，您可以使用 -d 标志（或 --detach）来运行 docker run 命令。这将使容器以分离模式运行，不会占用您的终端，并且容器将在后台持续运行。

以下是一个示例命令，将容器在后台运行：

```shell
docker run -d --name my-container my-image
```

在这个命令中：

-d 表示以分离模式（后台）运行容器。
--name my-container 为容器指定一个名称（这里是 "my-container"）。
my-image 是要运行的Docker镜像的名称。

```shell
docker stop my-container
```

停止名为 "my-container" 的容器。




## 查看Docker容器占用的内存

1. ### 使用 docker stats 命令：

docker stats 命令可以显示所有正在运行的容器的资源使用情况，包括内存、CPU、网络等。默认情况下，它将以实时模式显示容器的统计信息。

```shell
docker stats
```

这将显示一个表格，其中包括每个容器的内存使用情况（包括 使用的内存和限制的内存）。您可以按 Ctrl+C 来停止实时显示。

2. ### 使用 docker stats 查看特定容器的内存：

如果您只想查看特定容器的内存使用情况，可以提供容器的名称或ID作为参数：

```shell
docker stats my-container
```

这将显示名为 "my-container" 的容器的内存使用情况。

通过上述方法，您可以查看Docker容器的内存使用情况，以便监控和管理容器的资源。
