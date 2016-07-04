---
title: '在ARC项目中使用非ARC框架或者类库的解决方案 '
date: 2012-08-21 16:07:18
toc: false
categories: iOS & Mac
tags:
- iOS
- ARC
---

iOS 4引入了Automatic Reference Count（ARC），编译器可以在编译时对obj-c对象进行内存管理。

之前，obj-c的内存管理方式称作引用计数，就是obj-c对象每被”使用”一次,引用计数+1,当引用计数为0时,系统会回收内存.用程序语言 表达,就是allco的要release,retain/copy的要release.还有某些容器add的,也要release等等.

那么在现有的ARC项目中，如何引用非ARC的第三方框架或者类库呢？

答案很简单，只需在TARGETS里的Build Phases中，找到 Compile Sources，把涉及到非ARC的类后面加上`-fno-objc-arc`标志。如下图：

![](http://img9.ph.126.net/HXGs9V578ZKSc0uRRtUrmQ==/6597353440657067939.jpg)