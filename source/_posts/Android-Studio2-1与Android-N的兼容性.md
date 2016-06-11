---
title: Android N requires the IDE to be running with Java 1.8 or later
date: 2016-06-08 09:32:55
toc: true
categories: Android
tags:
- Android
- Android Studio
---

前段时间，下载了Android N Preview预览版的SDK，同时更新了Android SDK Tools到25.1.7.rc1，Android SDK Build-tools到24 rc4。之后好好体验了一把Android N以及Android N的官方demo『关于多窗口、通知栏、ScopeDirectory的示例』爽爆了。

## 问题

但是，最近打开老项目，打开布局文件预览时，发现预览不了了，出现如下提示： 

`Android N requires the IDE to be running with Java 1.8 or later`。

![AS-layout-preview1](http://7xsk2b.com1.z0.glb.clouddn.com/image/AS-layout-preview1.png)

当我点击提示中的 `Install a supported JDK` 链接后，发现是让安装JDK8，但我现在还不想构建Android N版本，AS稳定渠道还没有放出AS 2.2版本的升级包。我只想看到布局预览图就行了，这怎么办呢？

经过Google搜索，在[stackoverflow](https://stackoverflow.com/questions/35928580/android-n-requires-the-ide-to-be-running-with-java-1-8-or-later/35952099#35952099?newreg=70e51539b2cb4637ac632f12ab9c49a4)上找到了答案。

## 处理方案

![AS-layout-preview2.png](http://7xsk2b.com1.z0.glb.clouddn.com/image/AS-layout-preview2.png)

![AS-layout-preview3.png](http://7xsk2b.com1.z0.glb.clouddn.com/image/AS-layout-preview3.png)