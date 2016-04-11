---
title: Github+Hexo+travis
date: 2016-04-07 15:26:11
tags:
- Github
- Hexo
- Markdown
- 博客
- travis
---

## 引入travis-ci的背景

上一篇 [Github+Hexo+Markdown写博客](http://wangsheng.github.io/2016/04/06/Github-Hexo-Markdown%E5%86%99%E5%8D%9A%E5%AE%A2/)介绍了如何使用Github+Hexo搭建自己的博客，然后以Markdown方式写自己的博文。聪明的观众可能已经看出，写一篇博客，可能需要以下几步：

```
Hexo new "article" -> 写作 -> hexo generate -> git commit -> hexo deploy
```

这也太繁琐了吧，每次写完都需要自己执行generate动作和部署，有没有更方便的做法呢？

当然有，可以通过引入自动化集成CI来帮我们自动完成构建和部署。再来看看引入travis-ci后的步骤：

```
Hexo new "article" -> 写作 -> git commit -> git push
```

### 整体流程图

![github-markdown-wokeflow](http://zouzhi.net/images/work-flow.png)

### 环境准备

1. 使用Github账号授权Travis-CI

  使用Github账号登录[Travis-CI](https://travis-ci.org/)，并授权权限以及要进行CI持续化集成的版本库。

  ![travis-ci-authorize](http://7xsk2b.com2.z0.glb.clouddn.com/image/travis-ci-authorize.png)

  注意：为了防止后面，travis-ci会根据默认的配置，构建master分支，这里需要勾选中第一个：
  ![travis-ci-setting](http://7xsk2b.com2.z0.glb.clouddn.com/image/travis-ci-setting.png)

1. 进阶上一篇博客[Github+Hexo+Markdown写博客](http://wangsheng.github.io/2016/04/06/Github-Hexo-Markdown%E5%86%99%E5%8D%9A%E5%AE%A2/), 创建blog分支，用于存放博客站点源码以及markdown原始文件

  ``` bash
  Victors-MPB:wangsheng.github.io wangsheng$ git br
  * master
  Victors-MPB:wangsheng.github.io wangsheng$ git log
  commit 2a753ef09a01a93d722319fdcb7befcf32022e36
  Author: victor <wangsheng2008love@163.com>
  Date:   Mon Apr 11 11:24:42 2016 +0800

      add git deployer config.

  commit da14451775873ff390b95993d3bf74019bf3422a
  Author: victor <wangsheng2008love@163.com>
  Date:   Thu Apr 7 14:39:41 2016 +0800

      init blog.
  Victors-MPB:wangsheng.github.io wangsheng$ git co -b blog
  Switched to a new branch 'blog'
  ```

1. 创建travis-ci构建需要的配置文件

  ``` bash
  $ touch .travis.yml
  ```

1. 生成秘钥

  ``` bash
  $ ssh-keygen -t rsa -C "your_email@example.com"
  ```
  在生成过程中,passphrase 留空，因为travis-ci 中输入密码无法做到。同时使用默认的保存目录就好(~/.ssh/id_rsa)。

1. 添加生成好的 SSH key 到 ssh-agent

  ``` bash
  $ ssh-add ~/.ssh/id_rsa
  ```

1. 将公钥配置到 github 上项目的 deploy key 中

  ![github-deploy-key](http://7xsk2b.com2.z0.glb.clouddn.com/image/github-deploy-key.png)

  **注意：**这里需要选中 Allow write access

1. 将仓库的远程源改为ssh协议，避免https每次让输入用户账户信息

  ``` bash
  $ git remote add origin git@github.com:$username/$username.github.io.git
  ```

1. 将_config.yml中deploy小节中的地址也改为ssh协议

  ``` ruby
  deploy:
    type: git
    repo: git@github.com:$username/$username.github.io.git
    branch: master
  ```

### travis-ci命令安装与准备

1. 安装travis命令行工具

  本地安装 travis-ci。（travis 工具采用 ruby开发。需要本地安装有 ruby 开发环境）
  ``` bash
  $ gem install travis
  ```

1. 登录

  ``` bash
  $ travis login --auto
  ```
  这样通过 travis 提供的命令工具就可以加密私钥文件，并将加密用的密码存入 travis 服务器。以环境变量形式作为后面解密使用。

1. 私钥加密

  ``` bash
  $ travis encrypt-file ~/.ssh/id_rsa --add
  ```
  这时会生成加密后的密钥文件 id_rsa.enc。同时会将相关的指令插入.travis.yml文件中。

### 完善travis配置

1. 在根目录中生成一个.travis目录，将刚才生成的id_rsa.enc文件移动到这里，同时注意**修改.travis.yml中引用该文件的路径**

  ``` bash
  Victors-MPB:wangsheng.github.io wangsheng$ tree .travis
  .travis
  ├── id_rsa.enc
  └── ssh_config

  0 directories, 2 files
  ```

1. 同时在.travis 目录中创建一个名为ssh-config文件，内容如下

  ``` bash
  Host github.com
      User git
      StrictHostKeyChecking no
      IdentityFile ~/.ssh/id_rsa
      IdentitiesOnly yes
  ```

1. 添加travis的其他配置信息，最终.travis.yml文件如下：

  ``` ruby
  language: node_js

  node_js:
  - '0.12'

  before_install:
  - npm install hexo -g

  # Decrypt the private key 『这行命令是执行travis encrypt-file自动插入的，复制这里内容时，注意以你本地的为主。』
  - openssl aes-256-cbc -K $encrypted_112712e457d2_key -iv $encrypted_112712e457d2_iv -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
  # Set the permission of the key
  - chmod 600 ~/.ssh/id_rsa
  # Start SSH agent
  - eval $(ssh-agent)
  # Add the private key to the system
  - ssh-add ~/.ssh/id_rsa
  # Copy SSH config
  - cp .travis/ssh_config ~/.ssh/config
  # Set Git config
  - git config --global user.name "travis_robot"
  - git config --global user.email "email DOT xxx.com"

  script:
  - hexo generate
  - hexo deploy
  branches:
    only:
    - blog
  ```

### 查看结果

到这里，已经配置完毕，将改动提交，并执行push

``` bash
$ git add .
$ git commit -m "config travis"
$ git push origin blog
```

这时登录[Travis-CI](https://travis-ci.org)，可以看到会触发自动构建

![travis-ci-build](http://7xsk2b.com2.z0.glb.clouddn.com/image/travis-ci-build.png)

等构建完毕，就可以访问站点看效果 http://wangsheng.github.io/

