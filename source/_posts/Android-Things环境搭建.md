---
title: Android Things环境搭建
date: 2017-12-17 15:37:10
categories: IoT
tags:
- Android Things
- 物联网
- NXP i.MX7D
---

本例子中，以Pico Pro Maker Kit(不包含彩虹帽Rainbow HAT)介绍。

![Pico Pro Maker Kit](http://7xsk2b.com1.z0.glb.clouddn.com/image/Pico%20Pro.jpeg)

## 所含套件

- 1. Pico i.MX7 双核开发板
- 4. USB-C线
- 5. Wifi天线
- 6. 天线延长线
- 7. 摄像头
- 8. 摄像头视频线
- 9. 5寸多点触控显示屏
- 10. 显示屏6线电缆

![components](https://developer.android.com/things/images/imx7d-kit/inventory.jpg)

注: 我的不包含彩虹帽想关部件。

## 连接部件

### Wifi天线

1. 连接Wifi天线和天线延长线
![connect wifi antenna](https://developer.android.com/things/images/imx7d-kit/antenna_step1.jpg)

2. 将延长线另一端的小圆帽扣到图中制定位置上。
![fixed antenna cable to board](https://developer.android.com/things/images/imx7d-kit/antenna_step2.jpg)

### 摄像头

1. 手握主板，旋转右侧中间黑色固定边框，边框旋转起来后，右手将将视频线一端插入里面，最后将黑色边框再旋转下来，压紧视频线。
![Camera connect 1](https://developer.android.com/things/images/imx7d-kit/camera_step1.jpg)

2. 左手握视频线另一端，右手握蛇形头模块，并旋转起黑色边框，将视频线另一端插入，同样最后将黑色边框再旋转下来，压紧视频线。
![Camera connect 2](https://developer.android.com/things/images/imx7d-kit/camera_step2.jpg)

### 多点触控屏

1. 将主板背面朝上，并将黑色边框旋转起来。
![Display connect 1](https://developer.android.com/things/images/imx7d-kit/display_step1.jpg)

2. 将显示屏背面朝上，然后将视频线另一端插入卡槽，然后将黑色边框再旋转下来，压紧视频线。
![Display connect 2](https://developer.android.com/things/images/imx7d-kit/display_step2.jpg)

3. 将6线电缆一端插入图中的位置。
![Display connect 3](https://developer.android.com/things/images/imx7d-kit/display_step3.jpg)

4. 将主板和显示屏都调整为正面朝上，然后将6线电缆另一端插入图中位置
![Display connect 4](https://developer.android.com/things/images/imx7d-kit/display_step4.jpg)

### 最终谍照

![final result](http://7xsk2b.com1.z0.glb.clouddn.com/image/Pico%20Pro%20connected.jpeg)

如果你的开发板包含彩虹帽，可参考[官方连接教程](https://developer.android.com/things/hardware/imx7d-kit.html#connect_the_parts)

## 安装 Android Things

### NXP i.MX7D 介绍

The i.MX 7Dual delivers high-performance processing for low-power requirements with a high degree of functional integration. The i.MX 7Dual features an advanced implementation of two ARM®Cortex®-A7 cores, which operate at speeds of up to 1.2 GHz, as well as the ARM® Cortex®-M4 core. The Pico variant is pin-compatible with the Intel® Edison for sensors and low-speed I/O, but also adds additional expansion possibilities for multimedia and connectivity, giving you cutting edge technology that can easily be expanded and implemented for IoT designs.

![NXP i.MX7D Board](https://developer.android.com/things/images/nxp-pico7-board.png)

### 刷入镜像

#### (1). 硬件连接
1. 通过Type-C线，给主板上电
![power on](https://developer.android.com/things/images/pico7-connections.png)

2. 网络连接
这里强烈建议你将主板接入互联网，这样你的设备crash信息将会自动上报，同时也可以收到更新提示。
  - 通过网线上网
  - 通过Wi-Fi上网，这里需要你将Wifi天线接入主板
  ![wifi antenna](https://developer.android.com/things/images/pico7-antenna.png)

#### (2). 刷入Android Things

1. 从 [Android Things Console](https://partner.android.com/things/console/#/tools)下载Android Things启动工具。这里需要你登录Google账号，并接受服务条款。(注:自备梯子)
![console](https://developer.android.com/things/images/console/setup-utility.png)
2. 解压下载的zip包
3. 启动设置工具
  - Windows 双击可执行文件
  - Mac/Linux 在终端执行命令

~~~Shell
$ ~/Downloads/android-things-setup-utility/android-things-setup-utility-macos

Android Things Setup Utility (version 1.0.16)
============================
This tool will help you install Android Things on your board and set up Wi-Fi.

What do you want to do?
1 - Install Android Things and optionally set up Wi-Fi
2 - Set up Wi-Fi on an existing Android Things device
1
What hardware are you using?
1 - Raspberry Pi 3
2 - NXP Pico i.MX7D
3 - NXP Pico i.MX6UL
2
You chose NXP Pico i.MX7D.

Setting up required tools...
Fetching additional configuration...
Downloading platform tools...
File already downloaded.
Unzipping platform tools...
Finished setting up required tools.

Do you want to use the default image or a custom image?
1 - Default image: Used for development purposes. No access to the Android
Things Console features such as metrics, crash reports, and OTA updates.
2 - Custom image: Provide your own image, enter the path to an image generated
and from the Android Things Console.
2
Please enter the absolute path to the zip file containing your image:
/Users/wangsheng/Downloads/Victor_NXP Pico i.MX7D_1_userdebug_build.zip

Connect your device to this computer:
The USB cable should plug into your board's USB-C port. If your computer also
has USB-C ports like the more recent MacBooks, you will need to use a USB hub.
Otherwise the board won't power on correctly.

Once connected, press [Enter] to install Android Things on the device...

Looking for devices... This can take up to 3 minutes.
found device
Unzipping image...
Flashing Android Things. This will take a few minutes...
*Do not disconnect or interrupt!*

target reported max download size of 419430400 bytes
sending 'bootloader' (559 KB)...
OKAY [  0.018s]
......
rebooting...

finished. total time: 134.454s
Creating filesystem with parameters:
    Size: 1930952704
    Block size: 4096
    Blocks per group: 32768
    Inodes per group: 7872
    Inode size: 256
    Journal blocks: 7366
    Label:
    Blocks: 471424
    Block groups: 15
    Reserved block group size: 119
Created filesystem with 11/118080 inodes and 15505/471424 blocks

Successfully flashed your imx7d.
Successfully flashed Android Things...
Would you like to set up Wi-Fi on this device? (y/n)
~~~

这里可以选择n，完成Android Things刷入。然后通过屏幕UI界面，来设计Wi-Fi网络。

## 效果图

![Android Things run1](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI1.jpeg)
![Android Things run2](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI2.jpeg)
![Android Things run3](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI3.jpeg)
![Android Things run4](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI4.jpeg)
![Android Things run5](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI5.jpeg)
![Android Things run6](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI6.jpeg)
![Android Things run7](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI7.jpeg)
![Android Things run8](http://7xsk2b.com1.z0.glb.clouddn.com/image/Android%20Things%20UI8.jpeg)


