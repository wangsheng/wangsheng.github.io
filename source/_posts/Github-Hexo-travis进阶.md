---
title: Github+Hexo+travis进阶
date: 2016-04-11 18:26:11
toc: true
categories: Git
tags:
- Github
- Hexo
- Markdown
- 博客
- travis
- git
---

上一篇 [Github+Hexo+travis](http://wangsheng.github.io/2016/04/07/Github-Hexo-travis/)如何使用travis-ci自动构建静态博客。自由创客已经可以自由写作了，但是有些文艺青年觉得：

- 通过这样的域名访问，太low；
- 能不能换个主题；
- ...

下面一一给出方案

## 配置独立域名

### 注册域名

推荐[万网](http://www.net.cn/)（已被阿里收购）进行购买。当然国外的话[godaddy](https://www.godaddy.com/)也是不错的选择。具体的域名注册这里就不介绍了。

#### 域名解析

参见：https://support.dnspod.cn/Kb/showarticle/tsid/40/。

待域名解析成功后，我们进入博客目录，创建一个名为CNAME的文件

``` 
foo.bar
```

将CNAME同步到Github上

``` bash
$ git add .
$ git commit -m "Config Custom Domain"
$ git push
```

等待构建结束，你就可以通过独立域名：foo.bar 访问自己的博客了！

## 主题替换

Hexo 默认的主题是[Landscape](https://hexo.io/hexo-theme-landscape/)，如果感觉不喜欢，你可以基于默认主题进行修改，也可以访问官方的主题插件列表[hexo-theme](https://hexo.io/themes)，选择其他主题安装和替换。

例如选择[clean-blog主题](http://www.codeblocq.com/assets/projects/hexo-theme-clean-blog/)

1. 将主题clone到本地的hexo项目中

  ``` bash
  $ cd my-blog # 进入前面通过 hexo 创建的blog目录
  $ git clone https://github.com/klugjo/hexo-theme-clean-blog.git themes/clean-blog
  ```
  **注意：** 需要将themes/clean-blog目录下的.git目录删除

1. 修改_config.yml文件，将主题设置为clean-blog

  ``` ruby
  # Extensions
  ## Plugins: https://hexo.io/plugins/
  ## Themes: https://hexo.io/themes/
  theme: clean-blog
  ```

1. 启动 hexo 服务器，查看是否生效

  ``` bash
  hexo server
  ```
  访问查看 http://localhost:4000/

1. 提交，并将更改推送至远程Github

  ``` bash
  $ git add .
  $ git commit -m "use clean-blog theme"
  $ git push origin blog
  ```

## 添加评论功能

有人说，这样的静态的网站没有评论功能，不能跟读者互动多无聊呀。不过，不用担心，市场上有第三方的评论系统，可以直接以插件的方式接入。

下面介绍 [多说](http://duoshuo.com/)，具体的操作步骤参见 [Hexo使用多说教程](http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9)

```
注意：文章里介绍的配置多说short_name指你申请时的二级域名，如你申请了http://test.duoshuo.com
这个域名，那么你的short_name就是test
```

## 添加RSS订阅功能

看到别人的博客中都有RSS订阅功能，是不是手痒痒，也想给自己的博客搭建一个RSS订阅功能，方便别人使用RSS阅读器订阅呢？

Hexo核心并没有提供RSS订阅功能，不过有一个feed插件`hexo-generator-feed`，支持此功能。具体的配置过程可参见 [Hexo—正确添加RSS订阅](http://hanhailong.com/2015/10/08/Hexo%E2%80%94%E6%AD%A3%E7%A1%AE%E6%B7%BB%E5%8A%A0RSS%E8%AE%A2%E9%98%85/)
