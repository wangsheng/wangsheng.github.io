---
title: redmine与Gitlab集成
date: 2016-01-22 13:43:04
toc: true
categories: 项目管理
tags:
- Redmine
- Gitlab
---

Redmine 是一个灵活的项目管理与缺陷跟踪工具. 它是基于 Ruby on Rails 框架建立的Web的应用程序, 页面符合Web 2.0特性, 同时又简单易用, 给项目管理和进度度量带来极大的好处.GitLab，是一个利用 Ruby on Rails 开发的开源应用程序，实现一个自托管的Git项目仓库，可通过Web界面进行访问公开的或者私人项目。它拥有与GitHub类似的功能，能够浏览源代码，管理缺陷和注释。可以管理团队对仓库的访问，它非常易于浏览提交过的版本并提供一个文件历史库。团队成员可以利用内置的简单聊天程序（Wall）进行交流。它还提供一个代码片段收集功能可以轻松实现代码复用，便于日后有需要的时候进行查找。

## 问题

[better](http://www.iambetter.cn)这边一直使用Redmine作为项目管理和bug管理工具。公司Git仓库采用Gitlab来托管，而且Gitlab可以提供类似于Github的Code Review功能，这个是Redmine欠缺的地方。那么问题来了，我们的issues在redmine上，code以及Code Review在gitlab上，难道我们需要每次在redmine上领取任务后，都需要在gitlab上再创建一次同样的issues用于编码跟踪和Code Review么？答案当然是No！

Gitlab提供了与外部问题跟踪系统整合的接口，可以通过项目设置进行整合。

## 整合步骤

找到要整合的项目 -> Settings -> Services -> Redmine

具体配置参考以下截图：[redmine与Gitlab集成](https://raw.githubusercontent.com/51offer/51offer.github.com/blog/source/images/gitlab_join_redmine.png)

**注解**

- Activie: 是否是激活状态
- Description: 描述信息
- Project url: 这里填写Redmine中问题列表的url地址。『启用后，点击gitlab的issues菜单，会跳转到Redmine的问题列表页』
- Issues url: 这里填写Redmine中问题详情的url地址，只不够后面不是写死issue_id，而是用RESTful格式:id。『启用后Commits和Merge Request中关联的问题链接，点击后能直接跳转到Redmine上对应的问题详情页』
- New issue url: 这里填写Redmine中创建问题的url地址。『启用后，点击gitlab的创建问题链接，会进入Redmine的创建问题页面』
