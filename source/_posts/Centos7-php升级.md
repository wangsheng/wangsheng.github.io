---
title: Centos7 php升级
date: 2019-05-22 16:24:09
categories: PHP
tags: 
- Centos
- PHP
---

## 1. 检查当前安装的PHP包

~~~Shell
[root@VM_0_10_centos ~]# yum list installed | grep php
php70w-bcmath.x86_64               7.0.25-1.w7                 @webtatic
php70w-cli.x86_64                  7.0.25-1.w7                 @webtatic
php70w-common.x86_64               7.0.25-1.w7                 @webtatic
php70w-devel.x86_64                7.0.25-1.w7                 @webtatic
php70w-fpm.x86_64                  7.0.25-1.w7                 @webtatic
php70w-gd.x86_64                   7.0.25-1.w7                 @webtatic
php70w-mbstring.x86_64             7.0.25-1.w7                 @webtatic
php70w-mcrypt.x86_64               7.0.25-1.w7                 @webtatic
php70w-mysqlnd.x86_64              7.0.25-1.w7                 @webtatic
php70w-pdo.x86_64                  7.0.25-1.w7                 @webtatic
php70w-pear.noarch                 1:1.10.4-1.w7               @webtatic
php70w-pecl-igbinary.x86_64        2.0.1-1.w7                  @webtatic
php70w-pecl-imagick.x86_64         3.4.3-1.w7                  @webtatic
php70w-pecl-memcached.x86_64       3.0.3-2.w7                  @webtatic
php70w-process.x86_64              7.0.25-1.w7                 @webtatic
php70w-xml.x86_64                  7.0.25-1.w7                 @webtatic
~~~

## 2. 完全移除当前PHP安装包以免起冲突

~~~shell
[root@VM_0_10_centos ~]# yum remove php*
~~~

## 3. 查看当前yum源都提供了哪些可安装的PHP版本

~~~Shell
[root@VM_0_10_centos ~]# yum list php*
~~~

注意：默认的yum源无法升级PHP，需要添加第三方yum源，我们选择webtatic库

~~~shell
CentOs 5.x
rpm -Uvh http://mirror.webtatic.com/yum/el5/latest.rpm
CentOs 6.x
rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
CentOs 7.X
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
~~~

## 4、安装PHP及相关插件，这里以PHP7.1.x为例

~~~Shell
yum install php71w php71w-cli php71w-common php71w-devel php71w-embedded php71w-fpm php71w-gd php71w-mbstring php71w-mysqlnd php71w-opcache php71w-pdo php71w-xml php71w-ldap php71w-mcrypt php71w-bcmath
~~~

## 5、如果安装了PHP的扩展库，可以通过pecl命令安装

### 5.1 安装pecl

~~~Shell
# 如果php < 7
$ yum install php-pear

# 如果php >= 7
$ wget http://pear.php.net/go-pear.phar
$ php go-pear.phar
# 否则会报PHP syntax error, unexpected 'new' (T_NEW) in /usr/share/pear/PEAR/Frontend.php on line 91
~~~

### 5.2 安装mongo扩展

~~~Shell
$ pecl install mongodb
~~~

### 5.3 安装mosquitto扩展

~~~Shell
$ pecl install mosquitto -c channel://pecl.php.net/mosquitto-0.4.0
~~~

### 5.4 查看使用pecl安装的扩展有哪些

~~~shell
$ pecl list
Installed packages, channel pecl.php.net:
=========================================
Package   Version State
Mosquitto 0.4.0   beta
mongodb   1.5.3   stable
~~~

## 6、如果服务器运行了PHP的web程序，需要重启php-fpm

~~~Shell
[root@VM_0_10_centos ~]# systemctl restart php-fpm
~~~

## 7. 查看PHP版本

~~~shell
[root@localhost ~]# php -v
PHP 7.1.29 (cli) (built: May 13 2019 18:32:21) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.1.29, Copyright (c) 1999-2018, by Zend Technologies
~~~

