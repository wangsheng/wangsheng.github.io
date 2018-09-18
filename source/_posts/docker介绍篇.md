---
title: docker介绍篇
date: 2016-10-26 18:27:22
toc: true
categories: 大数据 & AI
tags:
- docker
---

说起[虚拟化](http://baike.baidu.com/view/729629.htm)技术，大家耳熟能详的有[VMware](http://baike.baidu.com/view/555554.htm)、[VirtualBox](http://baike.baidu.com/view/555554.htm)等等。但2013出来了一款开源、轻量、高效的后起之秀[Docker](http://baike.baidu.com/view/11854949.htm)。

## docker是什么？

**BUILD, SHIP, RUN**

Docker is the world’s leading software containerization platform.

![docker-visions](https://www.docker.com/sites/default/files/moby.svg)

## docker与传统VMware的区别

![VIRTUAL-MACHINES](https://www.docker.com/sites/default/files/WhatIsDocker_2_VMs_0-2_2.png)
<center>虚拟机</center>
虚拟机包含应用程序必须的二进制文件和所依赖的lib库，以及完整的客户机(相对于虚拟系统的宿主机)操作系统，所有这些加到一起达到几十GB。

![CONTAINERS](https://www.docker.com/sites/default/files/WhatIsDocker_3_Containers_2_0.png)
<center>容器</center>

容器包含应用程序和所依赖的库，但是容器之间是共享内核，运行在宿主操作系统分配的隔离的空间和进程。Docker容器不依赖于任何特定的基础设施：它们可以运行在任何计算机上，任何基础设备上，甚至任何云端。

更多关于docker与虚拟机的对比，可移步到[docker-vs-vm](https://www.sysgeek.cn/docker-vs-virtual-machine/)

## Docker架构

![docker-workflow](http://img.iaquam.com/image/jpg/docker-architecture.jpg)

Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。Docker 容器通过 Docker 镜像来创建。容器与镜像的关系类似于面向对象编程中的对象与类。

|Docker|面向对象|
|:--|:--|
|容器|对象|
|镜像|类|

Docker采用 C/S架构 Docker daemon 作为服务端接受来自客户的请求，并处理这些请求（创建、运行、分发容器）。 客户端和服务端既可以运行在一个机器上，也可通过 socket 或者RESTful API 来进行通信。
Docker daemon 一般在宿主主机后台运行，等待接收来自客户端的消息。 Docker 客户端则为用户提供一系列可执行命令，用户用这些命令实现跟 Docker daemon 交互。

## 安装与使用

### 下载安装包

[安装包下载](https://www.docker.com/products/docker)

### 安装

[安装步骤](https://docs.docker.com/docker-for-mac/) 

### 检测安装内容的版本信息

~~~shell
$ docker --version
Docker version 1.12.1, build 6f9534c
$ docker-compose --version
docker-compose version 1.8.0, build f3628c7
$ docker-machine --version
docker-machine version 0.8.1, build 41b3b25
~~~

### 常用命令

- 启动一个新的容器并运行命令 `docker run`
- 查看容器 `docker ps`
- 停止一个或者多个运行中的容器 `docker stop`
- 启动一个或者多个容器 `docker start`
- 列出本地镜像 `docker images`
- 删除本地镜像 `docker rmi`
- 删除容器 `docker rm`
