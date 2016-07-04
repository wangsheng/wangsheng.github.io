---
title: Mac 升级10.10 Yosemite系统 brew无法使用
date: 2015-01-11 21:23:58
toc: false
categories: iOS & Mac
tags:
- Mac
- Homebrew
- Ruby
---

今天休息在家，把自己的Macbook Pro升级到最新的操作系统 Yosemite。然后用Homebrew安装软件，却意外发现brew不能使用了，报了如下错误：

```
/usr/local/bin/brew: /usr/local/Library/brew.rb: /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby: bad interpreter: No such file or directory
/usr/local/bin/brew: line 23: /usr/local/Library/brew.rb: Undefined error: 0
```

上网上搜了下，才发现原因：原来Mac最新的Yosemite操作系统，默认安装了2.0版本的Ruby，而brew.rb中硬编码使用1.8版本的ruby，因此运行brew时报了找不到1.8版本ruby的错误。

因此解决方法也很简单，直接修改硬编码部分：

```
# vi /usr/local/Library/brew.rb
-#!/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -W0
+#!/System/Library/Frameworks/Ruby.framework/Versions/Current/usr/bin/ruby -W0
```
注：- 代表删除的行，+ 代表新加的行

最后最好执行下brew update，更新下索引库。
