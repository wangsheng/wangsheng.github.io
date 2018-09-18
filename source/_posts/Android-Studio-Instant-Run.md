---
title: Android Studio Instant Run
date: 2016-07-08 15:44:24
tags:
- Android Studio
- Android
---

随着代码量的增加，以及使用第三方库的增多，有时候改点代码，重新运行竟然花费Long Long time，这实在是浪费程序员的生命。于是，在Android Studio 2.0版本中，加入了吊炸天的功能`Instant Run`。有了这个法宝后，每次编译只会将改动的代码做成差异化文件，然后上传到调试设备上，与旧的app进行合并，生成新的app。有点类似差异化更新技术。这样改动代码，重新调试就是瞬间的事情了，真是谁用谁爽！

But，今天打开公司另一个团队的Android App源码，改动代码，运行，结果左下角出来如下提示：
![Android-studio-instant-run-tips](http://img.iaquam.com/image/Android-studio-instant-run-tips.png)

翻译过来：
```
Instant Run不支持部署在开启了Multidex模式的，并且target API等于或者低于20的Bild Variants上。
如果想在开启了Multidex模式上使用Instant Run功能，需要添加一个target API是21或者更高的Build Variant。
```

### 解决方法：

一个target API是21或者更高的Build Variant

```
android {

    ......

    productFlavors {
        instant {
            minSdkVersion 21
        }
        dev {
            minSdkVersion 17
        }

        ......
    }

    ......

}
```

![build-variants](http://img.iaquam.com/image/build-variants.png)

更多详细信息，可参考 [https://segmentfault.com/a/1190000004962523?utm_source=tuicool&utm_medium=referral](https://segmentfault.com/a/1190000004962523?utm_source=tuicool&utm_medium=referral)