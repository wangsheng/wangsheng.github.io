---
title: Centos7下安装Jenkins
date: 2017-11-01 10:32:12
categories: 项目管理
tags: 
- Centos
- Jenkins
- 持续集成
---

## 简介

Jenkins是一个用Java编写的开源持续集成工具。Jenkins提供了软件开发的持续集成服务,它运行在Servlet容器中（例如Apache Tomcat），支持软件配置管理（SCM）工具（包括AccuRev SCM、CVS、Subversion、Git、Perforce、Clearcase和RTC），可以执行基于Apache Ant和Apache Maven的项目，以及任意的Shell脚本和Windows批处理命令。

## 准备工作

### Java安装

1. [官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载Java rpm包。
2. 使用rpm命令安装
~~~Shell
rpm -ivh /path/to/jdk-xx-linux-xx.rpm
~~~
3. 检测java是否安装成功
~~~Shell
[root@192 victor]# java -version
java version "1.8.0_151"
Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)
~~~

## Jenkins安装

~~~Shell
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo # 下载依赖
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key # 导入秘钥
yum install jenkins # 安装
~~~

### 查看安装了那些文件

~~~Shell
[root@192 victor]# rpm -ql jenkins
/etc/init.d/jenkins
/etc/logrotate.d/jenkins
/etc/sysconfig/jenkins
/usr/lib/jenkins
/usr/lib/jenkins/jenkins.war
/usr/sbin/rcjenkins
/var/cache/jenkins
/var/lib/jenkins
/var/log/jenkins
~~~

主要目录解释

- /usr/lib/jenkins/ jenkins war包存放目录
- /etc/sysconfig/jenkins jenkins配置文件，常见的有：JENKINS_HOME(默认/var/lib/jenkins)、JENKINS_PORT(默认8080)

## Jenkins配置

### 1. 检测默认的8080端口是否被占用

~~~Shell
netstat -ltnp |grep 8080
~~~

如果有输出，则表明8080端口已被占用。需要编辑/etc/sysconfig/jenkins文件，找到JENKINS_PORT修改jenkins运行端口号。

### 2. 启动

~~~Shell
[root@192 victor]# java -jar /usr/lib/jenkins/jenkins.war --httpPort=8081
~~~

如果要以后台进程的方式启动，改成

~~~Shell
nohup java -jar /usr/lib/jenkins/jenkins.war --httpPort=8081 &
~~~

启动过程中，它会将war包解压到~/.jenkins目录下，并生成一些目录及配置文件。

### 3. 检测是否启动成功

浏览器输入:http://192.168.xxx.xxx:8081/

初次打开jenkins界面，系统会自动生成一个管理员密码(默认会写入/root/.jenkins/secrets/initialAdminPassword这个文件里)。找到这个文件，打开并copy密码，粘贴到输入框，点击 **Continue** 就能完成初次安全校验，进入管理系统了。

![初次打开](http://img.iaquam.com/image/jenkins-1.png)

## 初始化配置

### 1. 选择安装社区推荐的插件

![选择插件](http://img.iaquam.com/image/jenkins-2.png)

![安装](http://img.iaquam.com/image/jenkins-3.png)

### 2. 创建首个管理员账号

![创建管理员](http://img.iaquam.com/image/jenkins-4.png)

### 3. 点击 **Save and Finish**，完成初始化配置

![完成初始化](http://img.iaquam.com/image/jenkins-5.png)

![进入首页](http://img.iaquam.com/image/jenkins-6.png)

