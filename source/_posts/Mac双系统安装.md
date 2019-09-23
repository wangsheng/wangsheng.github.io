---
title: Mac双系统安装
date: 2019-09-20 21:23:58
toc: false
categories: Mac
tags:
- Mac
- Windows
---

## 一. 恢复出厂

**注意：如果新的电脑，可以跳过此步骤。**

对于旧的电脑，需要删除原有系统的所有的文件，可以通过系统自带的工具来恢复出厂设置。当然，如果需要备份的，请提前备份到别的磁盘。

1. 重启电脑并按下 `Command+R ` 组合键。
   ![img](https://pic2.zhimg.com/80/v2-7420d594cacc018dacb57d2e00e57821_hd.jpg)
2. 系统重启时，检测到该组合键被按下，会进入 `OS X Utilities` 界面。选择第四个 `磁盘工具` 菜单，并点击右下角的 `继续` 按钮。
   ![img](https://pic4.zhimg.com/80/v2-99863f45fcaf87a2d979f6b201ddbd17_hd.jpg)
3. 在磁盘管理界面，选择内置主硬盘名称，一般为 `Macintosh HD`，具体名称依电脑而异。接着选择`抹掉`，等待抹掉成功的提示。
   ![](http://img.iaquam.com/image/jpg/mac-join-windows-install-5.JPG)
4. 抹掉成功后，关闭对话框，返回 `OS X Utilities` 界面，选择第二个 `重新安装 macOS`菜单，并点击右下角的 `继续` 按钮。(**注意：重新安装时，必须保证电脑联网正常，且整个过程中需要保证电源供电状态。**)

## 二、安装Windows

### 1. 安装包准备

- windows镜像iso文件，没有镜像文件的，可访问 [itellyou](https://msdn.itellyou.cn/) 下载
- 如果只有WiFi，没有网线的话，还需要准备一个自带网卡版的驱动精灵安装包
- Windows激活工具

> 注意：此双系统安装方法，是利用Mac自带的 `启动转换助理` 工具安装的，关于macOS和Windows双系统的版本兼容性问题，请参见[官方说明](https://support.apple.com/zh-cn/HT201468)。

### 2. 开始安装

1. 打开Mac自带的  `启动转换助理` 工具，选择iso文件，并拖动分割线调整mac与Windows各自占用磁盘大小，然后点击 `安装` 按钮，后续根据提示进行操作即可。
   ![](http://img.iaquam.com/image/jpg/mac-join-windows-install-1.JPG)
2. 待  `启动转换助理` 工具完成磁盘分区，以及Windows安装文件的拷贝后，会自动进入安装Windows的流程。根据提示，进行操作即可。
   ![](http://img.iaquam.com/image/jpg/mac-join-windows-install-2.JPG)
3. 安装完成后，按照界面向导，进行初始化设置，包含登录账号和密码。
4. Windows安装完成后，使用激活工具，对Windows进行激活。
5. 使用驱动精灵，安装相应的驱动程序。
6. 使用Windows自带的磁盘管理工具，进行分区划分。因为默认只有一个C盘(BOOTCAMP卷)，最好再摘出来一个D盘。
   - 选择`BOOTCAMP卷`，点击右键，选择`压缩卷`
     ![](http://img.iaquam.com/image/jpg/mac-join-windows-install-3.JPG)
   - 然后输入压缩空间量(即新添加卷的大小)，默认的值为二等分BOOTCAMP卷的大小
   - 选择未分配的卷，点击右键，选择`新建简单卷`，根据向导完成设置
     ![](http://img.iaquam.com/image/jpg/mac-join-windows-install-4.JPG)

### 三、双系统之间的切换

重启电脑并按下 `Option`键，然后选择需要进入的系统。

### 四、注意事项

如果使用了iMac或者MacBook自带的蓝牙无线鼠标，在安装Windows之前，在macOS的 `系统偏好设置`里，把鼠标右键单击开启，否则进入Windows时，鼠标右键不起作用。
![](http://img.iaquam.com/image/jpg/mac-join-windows-install-6.JPG)