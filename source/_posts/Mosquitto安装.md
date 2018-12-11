---
title: Mosquitto安装
date: 2017-11-29 16:24:02
categories: IoT
tags:
- MQTT
- Mosquitto
- 物联网
- 消息队列
- 权限认证
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

## ACL 基于Topic的访问控制

Mosquitto安装完毕，默认包含一个aclfile.example的配置文件，内容如下：

~~~
# This affects access control for clients with no username.
topic read $SYS/#

# This only affects clients with username "roger".
user roger
topic foo/bar

# This affects all clients.
pattern write $SYS/broker/connection/%c/state
~~~

配置文件包含三种形式的访问配置：

- 第一段这种形式的声明，会影响到没有不含用户名的访问权限
- 第二段这种包含user的声明，会影响到具体指定用户的访问权限。这里user指令后面的用户需要是 pwfile 包含的用户。另外，topic [read|write|readwrite] <topic> 这里有读、写、读写三种权限，分别代表客户端对topic的订阅、发布、订阅和发布。如果不写中间的读写权限，则等同于readwrite权限。
- 第三段这种形式的声明，使用topic规则来验证，如果topic跟pattern里的匹配，则拥有该权限
  - `%c` 匹配客户端ID
  - `%u` 匹配客户端用户名

### 配置ACL

#### 1. 开启ACL

修改配置文件mosquitto.conf，添加以下内容

~~~
# config topic access control
acl_file /etc/mosquitto/aclfile
~~~

#### 2. 编辑acl配置

基于/etc/mosquitto/aclfile.example，复制一份，命名为/etc/mosquitto/aclfile。然后进行授权编辑。以下是我的测试配置:

~~~
# admin acl
user admin
topic readwrite /building/#

# sensor acl
user sensor
topic write /building/sensor/upload
topic read /building/sensor/fetch

# cloud acl
user cloud
topic read /building/sensor/upload
topic write /building/sensor/fetch
~~~

示例解析：

- admin具有 `/building/#` 订阅和发布的权限
- sensor具有 `/building/sensor/upload` 发布的权限，以及 `/building/sensor/fetch` 订阅的权限
- cloud具有 `/building/sensor/upload` 订阅的权限，以及 `/building/sensor/fetch` 发布的权限

也就是说，admin拥有整个建筑里数据访问协议；sensor拥有上传数据，以及接受云端指令的权限；admin有接受传感器数据和发布收集传感器数据指令的权限。这样即保证了整体的工作流程不受影响，也保证了账号独立，权限独立。

#### 3. 重新加载mosquitto，使acl配置生效

~~~shell
$ systemctl reload mosquitto
~~~

**注意:** 

- 如果开启acl，假设用户zhangsan存在于pwfile文件中，但是在aclfile里没有进行任何授权配置，那么默认是没有任何topic的订阅和发布权限。
- 另外，有一点比较坑，如果一个账户因为acl验证不通过，那么client收不到任何关于订阅失败或者发布失败的消息