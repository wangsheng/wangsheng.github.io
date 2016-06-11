---
title: 图片加载库Fresco
date: 2015-08-21 13:21:21
toc: true
categories: Android
tags:
- Android
- Fresco
---

## Fresco诞生背景

为提高Android中图片的加载速度，一般的图片库都采用了三级缓存：Memory Cache、Disk Cache和Network。但是，Android的系统层是将物理内存平均分配给每一个App。这样每个App所分配的空间都是有限的，早起的android设备，每个App只被分配16MB空间，这样，如果App中使用大量的图片，那么很容易因OOM而Crashes掉。Facebook App正是大量使用图片的App，面临这个问题刻不容缓，所以他们历尽艰难，开发了Fresco图片加载库。 
  
## Fresco诞生过程

### 内存区域分析：

* Java heap

  每个厂商会为App分配一个固定尺寸的运行空间。所有的申请通过Java的new操作申请，操作相对安全，通过GC内存自动回收保证内存不被泄露。但不幸的时，GC不够精确化，回收得不够及时。因此还是会存在OOM。
  
* Native heap

  通过C或者C++可绕过Java虚拟机直接操作物理内存，但Java程序员习惯了GC的自动回收，很难操作C++的手动操作内存。
  
* Ashmen

  Android还有一块内存区域，叫Ashmen。这里的操作很像Nativew heap，但是这里是系统调用的。Java 应用程序是不能直接访问Ashmen的，但是一些例外的情况可以操作，图片就是一种例外。

  ```Java
  BitmapFactory.Options = new BitmapFactory.Options();
  options.inPurgeable = true;
  Bitmap bitmap = BitmapFactory.decodeByteArray(jpeg, 0, jpeg.length, options);
  ```

### 难点突破：

尽管发现了Purgeable bitmaps，但是这个解码的过程是在UI线程操作的，因此他们又采用了异步实现，并保证了UI线程不引用时，unpin的区域不会被释放。

### 上层构建

提供给上层调用时，采用了MVC的架构：

* Model：DraweeHierarchy
* Control：DraweeControllers
* View：DraweeViews

## 使用示例

1. gradle配置中添加库引用

   ```
  compile 'com.facebook.fresco:fresco:0.6.1+'
  ```
2. xml中添加组件

   ```XML
  <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/sdv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        fresco:roundAsCircle="true"
        fresco:roundingBorderWidth="1dp"
        fresco:roundingBorderColor="#00ff00"
  ```
3. 代码中指定图片地址

   ```Java
   SimpleDraweeView sdv = (SimpleDraweeView) findViewById(R.id.sdv);
   sdv.setImageURI(Uri.parse("http://g.hiphotos.baidu.com/image/pic/item/2e2eb9389b504fc2b351980be7dde71190ef6db5.jpg"));
  ```

## 参考资料：
- [Fresco的由来](https://code.facebook.com/posts/366199913563917/introducing-fresco-a-new-image-library-for-android/)
- [Fresco的源码托管](http://github.com/facebook/fresco)
- [中文使用手册](http://fresco-cn.org/docs/index.html)
