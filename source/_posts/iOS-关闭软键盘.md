---
title: iOS 关闭软键盘
date: 2012-08-01 17:23:50
toc: false
categories: iOS & Mac
tags:
- iOS
- 软键盘
---

iOS中最简单的一行代码关闭当前界面的软件盘
```Objective-C
[self.view endEditing:YES];
```

说明：

self 为当前UIViewController的对象

view为当前UIViewController的根UIView
