---
title: Mosquitto安装
date: 2017-11-29 16:24:02
categories: IoT
tags:
- MQTT
- Mosquitto
- 物联网
- 消息队列
---

[Mosquitto](https://baike.baidu.com/item/mosquitto)是一款实现了消息推送协议 MQTT v3.1 的开源消息代理软件，提供轻量级的，支持可发布/可订阅的的消息推送模式，使设备对设备之间的短消息通信变得简单，比如现在应用广泛的低功耗传感器，手机、嵌入式计算机、微型控制器等移动设备。一个典型的应用案例就是 Andy Stanford-ClarkMosquitto（MQTT协议创始人之一）在家中实现的远程监控和自动化。并在 OggCamp 的演讲上，对MQTT协议进行详细阐述。

为每个MQTT消息头命令消息包含一个固定头，头只有两个字节，格式如下：
![MQTT-head](http://upload-images.jianshu.io/upload_images/2196419-e37a07c5eb9c558a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

## 安装

以Centos7.x为例

### 如果你服务器源里是 1.4.13 版本的 Mosquitto

~~~Shell
$ yum install mosquitto
~~~

### 如果你服务器源里是 1.4.14 版本的 Mosquitto

~~~Shell
$ yum install mosquitto mosquitto-clients
~~~

> [含有Mosquitto-v1.4.14版本的源](http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/home:oojah:mqtt.repo)

Mac用户可以通过Homebrew安装

~~~Shell
$ brew install mosquitto
~~~

## 简单测试

打开两个终端，一个模拟接收者，一个模拟发送者

### 接收者

~~~Shell
$ mosquitto_sub -t topic_test
~~~

### 发送者

~~~Shell
$ mosquitto_pub -t topic_test -m "Hello World."
~~~

这时候，接收者就会收到`Hello World.`的消息：

> [root@192 ~]# mosquitto_sub -t topic_test
> Hello World.

## 使用客户端工具来模拟收发

这里介绍一款Chrome插件 `MQTTBox`

### 配置如下
![MQTTBox-setting](http://img.iaquam.com/image/mqttbox_setting.png)

### 使用

添加一个订阅者
![add-subscriber](http://img.iaquam.com/image/add_subscriber.png)

添加一个发布者
![add-publisher](http://img.iaquam.com/image/add_publisher.png)

点击发布
![publish](http://img.iaquam.com/image/mqttbox_publish.png)

## 用户名密码认证

(1) 使用`Mosquitto`自带的`mosquitto_passwd`命令创建用户

~~~Shell
$ mosquitto_passwd -c /etc/mosquitto/pwfile test
Password:
Reenter password:
~~~

(2) 编辑`/etc/mosquitto/mosquitto.conf`配置文件，修改如下内容

~~~
allow_anonymous false
password_file /etc/mosquitto/pwfile
~~~

(3) 重启mosquitto

~~~Shell
$ systemctl restart mosquitto
~~~

这时候如果要测试发布和订阅，就需要设置用户名和密码了
![MQTTBox-setting-login](http://img.iaquam.com/image/mqttbox_setting_login.png)


