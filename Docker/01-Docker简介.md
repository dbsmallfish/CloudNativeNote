### Docker简介

> *官方文档：*[https://docs.docker.com/get-started/overview/](https://docs.docker.com/get-started/overview/)
> Dokcer 从入门到实践：[https://yeasy.gitbook.io/docker_practice/](https://yeasy.gitbook.io/docker_practice/)

#### Docker的组成

* Docker 主机（host）：一个物理机或虚拟机，用于运行Docker服务进程和容器。
* Docker服务端（server）：Docker守护进程，运行docker容器。
* Docker客户端（client）：客户端使用docker命令或其他工具调用docker api.
* Docker 镜像（image）：Docker镜像就是一个只读的模板。镜像可以用来创建Docker容器，一个镜像可以创建很多容器。
* Docker 容器（container）：Docker 利用容器 独立运行一个或一组应用。容器是从镜像生成对外提供服务的一个或一组服务。
* Docker 仓库（Registry）：仓库是集中存放镜像文件的场所。仓库分为公开仓库（Public）和私有仓库（Private）两种形式。最大的公开仓库是 Docker Hub([https://hub.docker.com/](https://hub.docker.com/))，国内的公开仓库包括阿里云 、网易云 等

#### Docker架构

![image-20211228121037141](/Users/chunlin.li/Library/Application Support/typora-user-images/image-20211228121037141.png)
