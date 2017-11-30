---
title: Mosquitto PHP扩展
date: 2017-11-30 11:57:51
categories: 大数据 & AI
tags:
- MQTT
- Mosquitto
- 物联网
- PHP
---

上篇[Mosquitto安装](http://victor87.coding.me/2017/11/29/Mosquitto%E5%AE%89%E8%A3%85/)介绍了如何安装以及命令行使用Mosquitto，那么PHP里如何调用Mosquitto呢？这篇文章将介绍PHP与Mosquitto的集成。

## 安装PHP扩展

[Mosquitto-PHP](https://github.com/mgdm/Mosquitto-PHP) is A wrapper for the Eclipse Mosquitto™ MQTT client library for PHP.

### 环境要求

- PHP 5.3+
- libmosquitto 1.2.x or later
- Mac or Linux

注：感谢[Sara Golemon](https://twitter.com/SaraMG)的贡献，使[pecl-mosquitto-v0.4.0](https://pecl.php.net/package-changelog.php?package=Mosquitto&release=0.4.0)已经支持PHP 7。

### 安装步骤

首先需要安装libmosquitto开发包，Red Hat系列的包名: `libmosquitto-devel`，Debian或者Ubuntu系列的包名: `libmosquitto-dev`。

以下以Centos 7.x为例:

#### 1. 安装 Mosquitto 开发包

如果像我一样，找不到`libmosquitto-devel`

~~~Shell
[root@192 ~]# yum search libmosquitto-devel
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.shuosc.org
 * epel: mirrors.tongji.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.shuosc.org
 * webtatic: sp.repo.webtatic.com
警告：没有匹配 libmosquitto-devel 的软件包
No matches found
~~~

可以添加含有MQTT的源

~~~Shell
[root@192 ~]# cd /etc/yum.repos.d/
[root@192 yum.repos.d]# wget http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/home:oojah:mqtt.repo
[root@192 yum.repos.d]# yum makecache
~~~

然后执行安装命令

~~~Shell
[root@192 ~]# yum install libmosquitto-devel
~~~

#### 2. 安装 Mosquitto PHP扩展

~~~Shell
[root@192 ~]# pecl install Mosquitto-0.4.0
~~~

这个提示直接按回车，自动探测就行。
> Please provide the prefix of the libmosquitto installation [autodetect] :

成功提示：
> Build process completed successfully
Installing '/usr/lib64/php/modules/mosquitto.so'
install ok: channel://pecl.php.net/Mosquitto-0.4.0
configuration option "php_ini" is not set to php.ini location
You should add "extension=mosquitto.so" to php.ini

#### 3. 配置 php.ini 文件

添加如下一行

~~~
extension=mosquitto.so
~~~

## 演示示例

本例子中Mosquitto的版本信息

~~~
mosquitto version 1.4.14 (build date 2017-09-14 18:40:30+0000)
~~~

### 1. 无认证方式

~~~PHP
<?php

$client = new Mosquitto\Client;
$client->onConnect(function() use ($client) {
    $client->publish('victor/test', 'Hello World.', 2);
});

$client->connect('127.0.0.1');

for($i = 0; $i < 100; $i++) {
    // 使用Loop循环来让Mosquitto处理自身事物
    $client->loop(1);
}

echo "Finished\n";
~~~

### 2. 认证方式

只需添加一行认证代码

~~~PHP
// 添加认证信息
$client->setCredentials('test', '123456');
~~~


