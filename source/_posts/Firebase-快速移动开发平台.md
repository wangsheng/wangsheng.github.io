---
title: Firebase-快速移动开发平台
date: 2016-05-19 17:35:24
toc: true
categories: Android
tags:
- Firebase
---

[Firebase](https://firebase.google.com/)是一个帮助你快速开发高质量的应用程序，增强你的用户基础，并提高盈利能力的移动开发平台。Firebase有很多互补性的功能组成，你可以根据具体需求，找到适用的功能模块搭配使用。

> 总体功能预览：

![all-feature-preview](https://lh3.googleusercontent.com/Jp5DG28Mj668TyylbnjcCjNvzh-9-IjxT1IixnKrOziswXJzQZZ8GUpRobmQPba0vvINC8c6GymEni3UYcAX3uLVdHFz0Z_x=s1600)

### 分析功能

Firebase的核心功能是Firebases分析，一个免费且没有限制的分析解决方案。通过一个统一的Dashboard看板，可以查看用户的行为以及属性的定量分析。同时支持iOS和Android平台：

- 最多可以无限制报告500个时间类型，每一个时间类型可支持25个属性
- 统一的dashbaord查看用户行为以及跨网络性能分析
- 人口分布，包括年龄、性别以及位置信息等
- 提供可导出的BigQuery自定义查询

### Develop

构建更好的应用程序，并预留接口给开发者。节省关键的开发时间，产出高质量、无bug的应用程序。

- 云端消息
提供可靠的跨平台的消息分发和接受通道
- 认证
提供健壮的认证机制
- 实时数据
实时存储和同步应用数据
- 存储
便捷的文件存储
- 寄主
快速分发网络内容
- 远程配置
可远程自定义应用的配置信息
- 测试Lab
提供云端测试功能
- Crash报告
保证应用的稳定性

### Grow

在恰当的时机，培养并吸引合适的用户。促进潜在用户的增长。

- 通知
恰当的时机吸引用户
- App索引
驱动有效的搜索流量到你的App
- 动态链接
应用内发送动态链接，引导用户到正确的地方
- 邀请
引导用户分享你的应用程序
- AdWords广告
通过Google搜索获取用户

### Earn

通过向全球用户展示吸引的广告赚取到收入。

- AdMob
通过吸引性的广告盈利

## 开发使用

### 前置条件

- 一个运行Google Play服务9.0.0以上版本的Android设备
- 通过 [Android SDK Manager](https://developer.android.com/tools/help/sdk-manager.html) 获取Google Play services
- [Android Studio](http://developer.android.com/sdk) 1.5或者更高版本
- 一个Android Studio项目和包名

> 注意事项：
> Android Studio低于2.2版本中的Instant Run存在Firebase Analytics和特定事件保护不兼容问题，官方建议禁用 Instant run或者升级Android Studio到2.2预览版。

### 创建Firebase项目

- 访问 [控制台](https://console.firebase.google.com/)，创建一个Firebase项目。
![firebase-create-projec](http://7xsk2b.com1.z0.glb.clouddn.com/image/firebase-create-project.png)
- 添加Firebase到应用程序
选择项目 -> 项目设置 -> 将Firebase添加到您的Android/iOS/网页应用，按照向导一步一步完成配置。
![firebase-console-dashboard](http://7xsk2b.com1.z0.glb.clouddn.com/image/firebase-console-dashboard.png)
![firebase-add-to-app](http://7xsk2b.com1.z0.glb.clouddn.com/image/firebase-add-to-app.png)
- 选择需要的功能模块，加入项目中
![firebase-feature-libs-list](http://7xsk2b.com1.z0.glb.clouddn.com/image/firebase-feature-libs-list.png)

具体的开发文档，[参见官网](https://firebase.google.com/docs)
