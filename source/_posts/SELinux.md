---
title: SELinux
date: 2017-08-11 17:37:29
categories: 网络安全
tags:
- 计算机基础
- PHP
- Nginx
---

前两天弄了一台配置不错的台式机，从centos官网下载了ISO镜像文件，然后找了一个U盘做成启动盘。系统安装完毕后，安装了开发环境(PHP7 & MariaDB & Nginx)，然后把手头项目部署上去。但是页面显示500错误，提示没有权限访问runtime目录，于是我立马想到了目录权限的问题。见鬼的是，当我把整个项目目录设置为777，用户属主也设置为当前运行的用户时，仍然提示这个错误。

接着就是google搜索，各种尝试，然并卵。。。然后放弃。

第二天继续尝试，无意间发现SELinux这个东西，检查了一下搭建的服务器，还真是开启了SELinux，于是关掉，重启，终于正常了。那么什么是SELinux呢？

## 概念

[SELinux(Security-Enhanced Linux)](https://baike.baidu.com/item/SElinux) 是美国国家安全局（NSA）对于强制访问控制的实现，是 Linux历史上最杰出的新安全子系统。NSA是在Linux社区的帮助下开发了一种访问控制体系，在这种访问控制体系的限制下，进程只能访问那些在他的任务中所需要文件。SELinux 默认安装在 Fedora 和 Red Hat Enterprise Linux 上，也可以作为其他发行版上容易安装的包得到。

## 启动、查看和关闭

目前 SELinux 支持三种模式，分别如下：

- enforcing: 强制模式, 代表SELinux运作中，且已经正确的开始限制domain/type了；
- permissive: 宽容模式, 代表SELinux运作中，不过仅会有警告讯息并不会实际限制domain/type的存取。这种模式可以运来作为SELinux的debug之用；
- disabled: 关闭，SELinux并没有实际运作。

### 查看SELinux的模式

~~~Shell
[root@localhost ~]# getenforce
Enforcing
~~~

### 查看SELinux的策略(Policy)

~~~Shell
[root@localhost ~]# sestatus
SELinux status:                 enabled    // 是否启动SELinux
SELinuxfs mount:                /selinux   // SELinux的相关文件资料挂载点
Current mode:                   enforcing  // 目前的模式
Mode from config file:          enforcing  // 设定档指定的模式
Policy version:                 21
Policy from config file:        targeted   // 目前的策略为何？
~~~

### 通过配置文件调整SELinux的参数

~~~Shell
[root@localhost ~]# vi /etc/selinux/config
SELINUX=enforcing     // 调整 enforcing|disabled|permissive
SELINUXTYPE=targeted  // 目前仅有 targeted 与 strict
~~~

### SELinux的启动与关闭

**上面是预设的策略与启动的模式！如果改变了策略则需要重新开机；如果由enforcing或permissive改成disabled，或由disabled改成其他两个，那也必须要重新开机。这是因为SELinux是整合到核心里面去的，你只可以在SELinux运作下切换成为强制enforcing或宽容permissive模式，不能够直接关闭SELinux的！**

同时，由SELinux关闭disable的状态到开启的状态也需要重新开机！所以，如果刚刚你发现getenforce 出现disabled时， 请到上述文件修改成为enforcing吧！

更多关于SELinux的设置，可参考 [SELinux 的启动、关闭与查看](http://blog.csdn.net/yujin2010good/article/details/7638983)
