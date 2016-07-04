---
title: 开发兼容 iOS retina 的程序，我们需要做什么?
date: 2012-08-15 13:36:00
toc: false
categories: iOS & Mac
tags:
- iOS
---

## 首先明确：
iPhone 3G/3GS 屏幕像素分辨率是   320×480 ;
iPhone4、iPod Touch4 屏幕像素分辨率 640×960。

为了兼容 iOS 4.0 之前的程序也能在 iOS 4 上运行，苹果设计了一个逻辑分辨率单位point ，在 iPhone3 上 1个 Point 相当于 1个pixel ; 而 iPhone4 上1个 point 就相当于4个 pixel；因此所有的iPhone、iPod Touch 设备的 Point 分辨率都是 320×480 ，也就是逻辑分辨率都一致，保证了App不需要修改也能正常的在高像素分辨率上运行，只是原来App中的图片会被拉升后显示，影响美观，没有发挥retina的优势。

## iOS App设计和开发人员要做什么？

1. App 的图标设计，发布到Store的App必须同时提供高清Size的App Icon（在原来基础上都要对应提供一份高清版本），参考Apple官方文档。

1. 代码中引用的静态UI 图片素材，也是提供两份，一份低像素分辨率，一份高分辨率使用。
比如：原来App素材包有个 demo.png ,那么 App bundle中就必须再提供一个两倍size的 demo.png , 并且文件命名为 demo@2x.png 后添加到项目工程中；

在代码中仍然这样写 [UIImage imageNamed:@"demo.png"] 即可, 无需修改代码，iOS系统可以自动对应不同屏幕取不同size的图像文件。

1. 如果App运行中从网络异步获取图片进行显示，或游戏App中动态生成图片后显示，需加上代码判断不同屏幕设备来获取/生成不同size图片。

```Objective-C
if ([[UIScreen mainScreen] respondsToSelector:@selector(scale)] && [[UIScreen mainScreen] scale] == 2){
//retina 或 ipad上启用2x显示iPhoneApp
//获取高清size图片
}
else {
//获取低清size图片
}
```

转自：[http://my.oschina.net/yongbin45/blog/69545](http://my.oschina.net/yongbin45/blog/69545)
