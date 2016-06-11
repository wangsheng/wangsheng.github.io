---
title: Java解析ISO-8601格式时间
date: 2016-06-08 16:58:32
toc: true
categories: Java
category:
- Java
tags:
- Java
---
# Java如何解析ISO-8601格式的时间？

`例如：2016-06-08T16:58:23.000+08:00`

## 什么是ISO-8601格式时间

国际标准化组织的国际标准ISO 8601是日期和时间的表示方法，全称为《数据存储和交换形式·信息交换·日期和时间的表示方法》。目前最新为第三版ISO8601:2004，第一版为ISO8601:1988，第二版为ISO8601:2000。更多信息，参考[百度百科](http://baike.baidu.com/link?url=VkPe3fh5z95-CAOe95Sf88Tx6bteuAaNT3HRvPpjlFtModhffNHIMFD69eBbXk5Uw3Jpclma4HQ6_-WS-ryVWa) 或者 [维基百科](https://en.wikipedia.org/wiki/ISO_8601)

## 解析方法

```Java
String str = "2016-06-08T16:58:23.000+08:00";
SimpleDateFormat sdfSource = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSSX");//原始的日期格式
SimpleDateFormat sdfDest = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS Z");//目标日期格式
try {
    System.out.println(sdfDest.format(sdfSource.parse(str)));
} catch (ParseException e) {
    e.printStackTrace();
}
```

执行结果:
> /Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home/bin/java

> 2016-04-01 17:43:23.000 +0800
>
> Process finished with exit code 0