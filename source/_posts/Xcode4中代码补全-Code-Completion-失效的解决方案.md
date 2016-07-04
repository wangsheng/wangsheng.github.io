---
title: Xcode4中代码补全(Code Completion)失效的解决方案
date: 2012-07-27 15:30:26
toc: false
categories: iOS & Mac
tags:
- iOS
- Xcode
---

以前好像很少碰到Xcode中代码提示出问题的情况，即使碰到了大多也是后来自然的就好了，最近换用了Xcode4.3，经常遇到这个问题。

通过无所不能的谷歌大神，找到了苹果论坛上提供的一个解决方案

[https://discussions.apple.com/thread/2746273?start=0&tstart=0](https://discussions.apple.com/thread/2746273?start=0&tstart=0)

1. cd进入~/Library/Developer/Xcode/DerivedData
1. ls 一下
1. 找到你项目所用的目录（一般以你的项目名开头）
1. cd 目录名
1. rm -r Index 删除掉你的项目所用的索引文件夹

或者在Xcode -> Window -> Organizer -> Projects 选中你的项目，点击如下图Derived Data右侧的Delete按钮

注：
1. 原文表示删除~/Library/Developer/Xcode/DerivedData下所有的文件，我尝试发现只需要删除当前项目相关的索引文件即可
1. DerivedData从字面上理解应该是收集到的数据，应该是Xcode针对这个项目缓存的一些数据，不会影响项目本身的完整性

转自：[http://www.1mima.com](http://www.1mima.com/xcode4%E4%B8%AD%E4%BB%A3%E7%A0%81%E8%A1%A5%E5%85%A8code-completion%E5%A4%B1%E6%95%88%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)
