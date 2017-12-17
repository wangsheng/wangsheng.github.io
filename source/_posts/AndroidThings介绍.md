---
title: Android Things介绍
date: 2017-12-16 12:54:39
categories: IoT
tags:
- Android Things
- 物联网
---

PC、智能手机时代已过去，接下来进入的是一个万物联网的时代，即物联网时代。
![IoT增长](http://img4.iyiou.com/Editor/image/20161012/1476262106932494.jpg)

从图中不难看出物联网的量级要远远超过之前的PC和智能手机数量。因此 [Google](https://google.com)自然不甘错失机会，推出了 [Android Things](https://developer.android.com/things/get-started/index.html)。

Android Things是一个可以用来构建专业的、大众消费品的可信赖平台，同时该平台不需要多年的嵌入系统设计经验。它减少了大量的前期开发成本，同时减少了验证想法是否具有现实可操作性的风险。当你准备大批量产设备时，你的成本也会线性增长，而正在进行的工程和测试成本会随着谷歌提供的更新越来越小。

## 硬件

目前与Google合作提供Android Things SoMs认证以及载板的合作伙伴有：

- 集成部分 - SoMs集成了SoC (System-on-chip), RAM, flash storage, WiFi, Bluetooth和其它组件于单一板子上，并且通过了所有FCC需要的认证。当你需要批量生产时，你可以通过将现有模块扁平化到PCB上来优化你的电路板设计，从而节省成本和空间。
- Google BSP - 板载支持包受到Google的管理，提供标准的更新以及Bug修复，从而给开发者一个可信赖的平台。
- 差异化的硬件 - Google的合作伙伴提供携带不同SoMs和形式因素来满足你的需要，让你的选择更灵活。

查看SoMs以及板载 [开发工具](https://developer.android.com/things/hardware/index.html)

## SDK

Android Things继承Android框架的核心，并额外提供移动设备所不具备的的Things支持库。

![Android Things Framework](https://developer.android.com/things/images/platform-architecture.png)

相移动设备来说，开发嵌入系统的App主要有以下几个不点：

- 比移动设备更灵活的访问外围设备和驱动
- 移除系统应用，从而优化启动和存储需要
- 设备启动时App自动打开，让用户沉浸在应用程序体验中
- 不像移动设备那样给用户提供了多个App，而Android Things设备只暴露一个应用给用户

想了解更多Android Things与Android框架的相似和区别，可访问[SDK Overview](https://developer.android.com/things/sdk/index.html)

## 控制台

![Android Things Console](https://developer.android.com/things/images/console/console-home.png)

Android Things控制台提供了安装和更新系统镜像的工具，供你开始构建原型和设备。控制台支持你推送OTA给指定用户和设备。

- 下载和安装最新的 Android Things 系统镜像
- 构建包含OEM应用的生产镜像
- 推送包含OEM应用和系统镜像的 OTA 更新

了解更多特性，可访问 [Console document](https://developer.android.com/things/console/index.html)

想上手把玩下Android Things，可访问 [Android Things环境搭建](http://victor87.coding.me/2017/12/17/Android-Things%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/)




