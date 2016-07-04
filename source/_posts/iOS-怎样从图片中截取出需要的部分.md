---
title: iOS 怎样从图片中截取出需要的部分
date: 2012-08-17 17:01:05
toc: false
categories: iOS & Mac
tags:
- iOS
- 图片截取
---

iOS 怎样从图片中截取出需要的部分

```Objective-C
/**
*从图片中按指定的位置大小截取图片的一部分
* UIImage image 原始的图片
* CGRect rect 要截取的区域
*/
- (UIImage *)imageFromImage:(UIImage *)image inRect:(CGRect)rect {    
 CGImageRef sourceImageRef = [image CGImage];    
 CGImageRef newImageRef = CGImageCreateWithImageInRect(sourceImageRef, rect);    
 UIImage *newImage = [UIImage imageWithCGImage:newImageRef];    
 return newImage;    
}  
``` 
