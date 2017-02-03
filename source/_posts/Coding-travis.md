---
title: Coding+travis
date: 2017-02-03 18:12:42
categories: Git
tags:
- Github
- Coding
- Hexo
- Markdown
- 博客
- git
---

之前的系列文章介绍了如何使用[Markdown+Hexo+Github+Travis](http://victor87.coding.me/2016/04/07/Github-Hexo-travis/)写静态博客，并自动部署到github.io上面。但是接下来你就会发现两个问题：

1. 由于托管在国外的Github上，访问速速太慢。
1. 由于github屏蔽了百度爬虫，所以百度搜不到博客。

那么，有没有办法解决这个问题呢？

经常用git的同学，肯定能想到，用国内的coding.net来代替github呀。对，就是这个思路。于是我看了一下[travis的部署文档](https://hexo.io/docs/deployment.html)，发现travis本来就支持多个云端自动部署。那这个问题就好解决了。

## 在码市[coding.net](https://coding.net/)上创建一个个人主页的git仓库。

说明，码市和github类似，都支持个人用户级别和项目级别的Pages服务，详情参见[Coding Pages](https://coding.net/help/doc/pages/index.html)。
> 如果您希望创建「用户 Pages」（直接访问 {user_name}.coding.me 即可抵达您网站），只需要新建一个名为 {user_name}.coding.me 的项目即可。{user_name} 指代您本人的个性后缀，使用其他人的个性后缀不会被归为「用户 Pages」类型。

## 授权travis能访问仓库{user_name}.coding.me

1. 添加部署公钥到 {user_name}.coding.me 项目中

项目列表 -> {user_name}.coding.me -> 设置 -> 部署公钥 -> 将之前travis部署用到的公钥添加到这里。

2. 修改配置文件.travis/ssh_config，让travis主机默认信任coding.net

~~~
Host github.com
        User git
        StrictHostKeyChecking no
        IdentityFile ~/.ssh/gt_blog_id_rsa
        IdentitiesOnly yes
Host git.coding.net
        User git
        StrictHostKeyChecking no
        IdentityFile ~/.ssh/gt_blog_id_rsa
        IdentitiesOnly yes
~~~

## 修改部署文件_config.yml，让travis同时部署到coding上

~~~
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
- type: git
  repo: git@github.com:wangsheng/wangsheng.github.io.git
  branch: master
- type: git
  repo: git@git.coding.net:victor87/victor87.coding.me.git
  branch: master
~~~

## 将这些配置信息入库，提交，并push到github上，从而触发travis自动构建和部署

![travis-join-coding1](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding1.png)

红色框中说明，travis已自动将静态博客内容同时部署到了github和coding上了。

## 配置coding的pages服务

这时登录coding，访问{user_name}.coding.me，可以看到博客内容已经入库了。然后就选择源，部署pages服务。

![travis-join-coding2](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding2.png)
![travis-join-coding3](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding3.png)
![travis-join-coding4](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding4.png)

看效果

![travis-join-coding5](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding5.png)
![travis-join-coding6](http://7xsk2b.com1.z0.glb.clouddn.com/image/travis-join-coding6.png)