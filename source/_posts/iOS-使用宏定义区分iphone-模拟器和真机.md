---
title: iOS 使用宏定义区分iphone 模拟器和真机
date: 2012-08-27 14:46:09
toc: false
categories: iOS & Mac
tags:
- iOS
- 宏定义
---

```Objective-C
#if TARGET_IPHONE_SIMULATOR

#import "Release-iphonesimulator/BaiduMobStat.h"

#elif TARGET_OS_IPHONE

#import "Release-iphoneos/BaiduMobStat.h"

#endif
```
