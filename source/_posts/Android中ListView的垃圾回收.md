---
title: Android中ListView的垃圾回收
date: 2015-08-07 13:45:55
toc: true
categories: Android
tags:
- Android
---

『ListView中ItemView的复用』为了提高ListView的加载速度和用户操作的流畅度，ListView底层做了ItemView的复用，避免重复地为每一个ItemView开辟空间。

- ActiveView
  
  激活view，当期显示在屏幕上的激活的view
- ScrapView
  废弃view，被删除的ActiveView会被自动加入ScrapView
  
## ListView的ItemView中大图片的覆盖显示

由于复用机制，当Item中包含大图时，由于Java GC的回收机制不够及时，造成ScrapView中图片资源不能立即释放，当用户上拉加载新的item内容时，却看到了之前加载的图片，直到新图从网络下载完毕，才会刷新view正确显示图片。

考虑到这种情况，Android 在API Level1中已经为ListView和GridView添加了RecyclerListener回调接口。开发者只需为ListView注册回调，重写onMovedToScrapHeap方法即可。

示例：

```Java
mListView.setRecyclerListener(new RecyclerListener() {
        @Override
        public void onMovedToScrapHeap(View view) {
            // Release strong reference when a view is recycled
            final ImageView imageView = (ImageView) view.findViewById(android.R.id.icon);
            imageView.setImageBitmap(null);
        }
    });
```


## 参考资料：
- [ListView回收机制相关分析](http://www.cnblogs.com/qiengo/p/3628235.html)
- [Listview items remove bitmaps from memory when the user scrolls](http://stackoverflow.com/questions/14238532/listview-items-remove-bitmaps-from-memory-when-the-user-scrolls)
