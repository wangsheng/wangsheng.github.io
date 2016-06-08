---
title: Android动画制作
date: 2015-07-07 18:27:22
categories: Android
tags:
- Android
- 动画
---

## View Animation

- AlphaAnimation 淡入淡出效果
- TranslateAnimation 移动效果
- ScaleAnimation 缩放效果
- RotateAnimation 旋转效果

原理：提供动画的起始和结束状态信息，中间的状态根据上述类里差值器算法填充
[[示例参考]](http://www.cnblogs.com/angeldevil/archive/2011/12/02/2271096.html)

## Property Animation

View Animation动画比较简单，一般都是单个因素的变化，如果牵扯到复杂的动画，就显得力不从心。因此，Android 3.0 引入了属性动画。注：可通过[[NineOldAndroids]](http://nineoldandroids.com/)项目在3.0之前的系统中使用Property Animation

原理：暴露出差值算法的回调方法，有工程师自己发挥想象，造出奇妙的动画效果。
[[示例参考]](http://www.cnblogs.com/angeldevil/archive/2011/12/02/2271096.html)

## Drawable Animation

### 逐帧动画

原理：提供动画每个帧上的图片资源，顺序播放
[[示例参考]](http://www.cnblogs.com/angeldevil/archive/2011/12/02/2271096.html)

### ClipDrawable 剪切动画

原理：提供一个背景图片，前景图片是一个ClipDrawable对象。通过线程操作ClipDrawable的剪切进度。

实例：

- ClipDrawable 定义xml文件
 
~~~XML
<?xml version="1.0" encoding="utf-8"?>
<clip xmlns:android="http://schemas.android.com/apk/res/android"
    android:clipOrientation="vertical"
    android:drawable="@drawable/loading_progress"
    android:gravity="bottom">
</clip>
~~~

- 组件引用

~~~ XML
<ImageView
        android:id="@+id/iv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:scaleType="centerInside"
        android:paddingTop="5dp"
        android:paddingLeft="5dp"
        android:background="@drawable/loading_bg"
        android:src="@drawable/clip_loading"/>
~~~

- 线程动态改变剪切进度

``` Java
private Handler handler = new Handler() {
	@Override
    public void handleMessage(Message msg) {
	    // 如果消息是本程序发送的
        if (msg.what == MSG_WHAT) {
            mClipDrawable.setLevel(mProgress);
        }
    }
};
.......
Runnable mRunnable = new Runnable() {
	@Override
    public void run() {
        isRunning = true;
        while (isRunning) {
            handler.sendEmptyMessage(MSG_WHAT);
            if (mProgress > MAX_PROGRESS) {
                mProgress = 0;
            }
            mProgress += 100;
            try {
                Thread.sleep(18);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
};
```
