---
title: GitLab持续集成CI
date: 2019-02-21 18:55:51
categories: Git
tags: 
- GitLab
- git
- 持续集成
---

在软件开发中，对于开发人员来说，负责编写代码来完成需求的开发或者Bug的修复。如果是Web类型的应用，开发结束后，如何发布供用户访问呢？

在传统的方式中，每次开发完代码后，由开发将代码转交给运维人员，然后由运维人员来将代码发布到Web服务器上，供用户访问使用。

但是互联网讲究的是敏捷开发，所以要面临频繁的发版需求；再加上一般分为测试环境(test)、预发布环境(pre)和正式环境(pro)。这样一来，高频次发版 + 多环境发布使这种跨部门的传统发布模式与敏捷开发的思想格格不入。那么，有没有一种自动化的方案来完成这样重复无味的工作呢？

如果你们使用Git作为版本控制工具，且使用GitLab作为源码托管的话，那么就可以使用GitLab(8.0及以上版本)自带的CI拓展功能完成自动化构建和部署，即CI(Continuous Integration)/CD(Continuous Delivery)。

## 名词解释

### GitLab-CI

GitLab从8.0版本开始，推出了自动化集成和部署的解决方案：[Gitlab-CI](https://docs.gitlab.com/ce/ci/quick_start/README.html)，并且对现有项目默认开启。如果项目中需要自动化集成，那么只需要在项目仓库的根目录添加 `.gitlab-ci.yml`文件，指定执行构建的 [GitLab-Runner](https://docs.gitlab.com/ce/ci/runners/README.html) ，那么之后每一次合并请求(MR)或者推送代码(Push)都会触发CI的[任务流水线(Pipeline)](https://docs.gitlab.com/ce/ci/pipelines.html)。

### GitLab-Runner

Gitlab-Runner 是具体执行构建和部署任务的执行器。Runner可以跟Gitlab安装台同一台机器上，但是考虑到Runner的资源消耗问题和安全性问题，官方不建议将Runner和Gitlab安装台一台物理机上。

Runner又分为两种：

- Shared Runners 可以运行开启了 `Allow shared runners` 选项的所有项目
- Specific Runners 只能运行指定的项目 

### 任务流水线 Pipelines

![](https://docs.gitlab.com/ce/ci/img/types-of-pipelines.png)

Pipelines是指不同阶段的一组任务。同一个阶段的任务可以并行执行(如果有足够多的并行Runner)，如果此阶段的任务都执行成功，则流水线会自动进入下一个阶段。如果某一个任务执行失败，则下一阶段的任务不会执行。可以通过点击管理界面中的Pipelines标签，来查看GitLab上的流水线页面。

#### 开发工作流

![](https://docs.gitlab.com/ce/ci/img/pipelines-goal.png)

1. Branch Flow： 例如基于dev、qa、staging、production的不同分支
2. Trunk-based Flow： 例如特性分支和单个master分支，可能带有发布tag
3. Fork-based Flow： 例如来自于forks的合并请求

### 徽章 Badges

GitLab从10.7开始，引入徽章的感念。它包含Pipeline状态以及测试覆盖报告等类型的徽章。

![](http://img.iaquam.com/image/png/gitlab-badges.png)

## 1. 安装 GitLab-Runner

以Centos为例，其他机型参见[官方文档](https://docs.gitlab.com/runner/install/linux-repository.html)

1. 添加GitLab官方仓库
   ~~~shell
   curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash
   ~~~
2. 安装最新版本的GitLab Runner，如果需要安装制定版本的Runner，可以跳过此步
   ~~~shell
   sudo yum install gitlab-runner
   ~~~
3. 安装指定版本的GitLab Runner
   ~~~shell
   sudo yum list gitlab-runner --showduplicates | sort -r
   sudo yum install gitlab-runner-10.0.0-1
   ~~~
4. 升级GitLab Runner
   ~~~shell
   sudo yum update
   sudo yum install gitlab-ci-multi-runner
   ~~~
   
## 2. 注册GitLab-Runner

### 注册一个共享Runner

1. 访问GitLab的 `admin/runners` 页面，获取共享Runner的token
   ![](https://docs.gitlab.com/ee/ci/runners/img/shared_runners_admin.png)

2. 注册Runner
   从GitLab8.2开始，共享Runners默认是开启状态。但是你可以通过每个项目的 `Settings -> CI/CD`页面手动禁用。之前版本的共享Runners默认是禁用状态。

   以GNU/Linux为例，以下是注册的操作步骤：

   1. 运行以下命令

      ~~~Shell
       sudo gitlab-runner register
      ~~~

   2. 填写GitLab示例URL

      ~~~Shell
       Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
       https://gitlab.com
      ~~~

   3. 填写GitLab-CI的token

      ~~~shell
      Please enter the gitlab-ci token for this runner
       xxx
      ~~~

   4. 给Runner起一个名字，以后也可以通过GitLab的UI页面修改此名字

      ~~~shell
       Please enter the gitlab-ci description for this runner
       [hostame] my-runner
      ~~~

   5. 给Runner打一个标签，以后也可以通过GitLab的UI页面修改此标签

      ~~~shell
       Please enter the gitlab-ci tags for this runner (comma separated):
       my-tag,another-tag
      ~~~

   6. 输入Runner的执行器

      ~~~shell
       Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
       docker
      ~~~

   7. 如果你选择Docker作为执行器，注册引导程序会问你要docker的镜像文件。

      ~~~shell
       Please enter the Docker image (eg. ruby:2.1):
       alpine:latest
      ~~~

      ![](http://img.iaquam.com/image/png/gitlab-runner1.png)

### 注册一个特定Runner

可以通过以下两种方式，创建特定Runner：

1. 使用项目级别的注册token创建一个Runner
2. 转换一个共享的Runner为特定Runner(这种方法需要你是平台管理员)

更多详细资料，请参见[官方文档](https://docs.gitlab.com/runner/register/index.html)

## 3. 配置CI

对于GitLab里的项目，如果想启用持续集成。只需在项目仓库的根目录下创建一个 `.gitlab-ci.yml`即可。关于此配置文件的全部介绍，可参见[官方文档](https://docs.gitlab.com/ce/ci/yaml/README.html)。

以下是我使用`vue-admin-template` demo项目作为练手，配置持续集成和持续部署的配置文件如下：

~~~yaml
cache:
  paths:
    - node_modules/

stages:
  - build
  - test
  - deploy

build:
  stage: build
  tags:
    - shell
  script:
    - npm install --registry=https://registry.npm.taobao.org
    - npm run build
  artifacts:
    name: "$CI_JOB_NAME-$CI_COMMIT_REF_NAME"
    paths:
      - dist/
    expire_in: 3 days

test:
  stage: test
  tags:
    - shell
  script:
    - echo 'execute test...'
  when: on_success

deploy_test:
  stage: deploy
  tags:
    - shell
  script:
    - rsync -avzh --progress --stats --delete ./dist/ "$SRV_PATH_TEST"
  environment:
    name: test
  only:
    - dev

deploy_pre:
  stage: deploy
  tags:
    - shell
  script:
    - rsync -avzh --progress --stats --delete ./dist/ "$SRV_PATH_PRE"
  environment:
    name: pre 
  only:
    - pre

deploy_pro:
  stage: deploy
  script:
    - rsync -avzh --progress --stats --delete ./dist/ "$SRV_PATH_PRO"
  environment:
    name: pro 
  only:
    - master
  when: manual
~~~

配置文件解读：

- cache 配置构建完后需要保存的文件。为了加快构建，需要缓存node_modules目录的文件，否则每次npm install 都需要重新安装一遍，太慢。
- stages 描述CI/CD流水线一共几个阶段，这里可以看到，我指定了三个阶段：build、test、deploy。
- build 属于构建任务(Job)
  - stage 指定任务所属的阶段
  - tags 指定可以执行此构建任务的runner需要含有的标签
  - script 具体执行构建的脚本指令
  - artifacts 构建产生的目标产物
    - name 指定目标产物压缩包的名字
    - paths 指定目标产物的路径
    - expire_in 指定目标产物保存的时长，超过时长后会自动删除
- test 属于测试任务(Job)
  - when `on_success`只有当先前阶段的所有任务都执行成功，才会触发本任务
- deploy_test 测试环境部署任务(Job)
  - script 这里使用rsync工具，将构建产物同步到Web服务器代码目录里。`$SRV_PATH_TEST`属于自定义的私密变量，可在GitLab上`group_name/project_name/settings/ci_cd`页面中进行设置。另外，此页面底部有关于构建和测试覆盖的徽章的设置代码，可以拷贝到项目根目录的README.md文档中，这样就能查看项目构建动态以及测试覆盖度了。
    ![](http://img.iaquam.com/image/png/gitlab-secret-variable.png)
  - environment 部署环境，可在GitLab上`group_name/project_name/environments`页面中设置。
    ![](http://img.iaquam.com/image/png/gitlab-environments.png)
    - name 当前构建任务对应的环境，需要跟页面中设置的一致
  - only 指定只运行哪些分支或者标签(tag)的代码。这里的`dev`表示，当dev分支上有代码变更时触发此部署。
- deploy_pre 预发布环境部署任务(Job)
  - only 这里的`pre`表示，当pre分支上有代码变更时触发此部署。
- deploy_pro 生产环境部署任务(Job)
  - only 这里的`master`表示，当master分支上有代码变更时触发此部署。
  - when `manual`表示生产环境的部署任务，需要手动来触发。

运行效果图：

- CI/CD流水线列表
  ![](http://img.iaquam.com/image/png/gitlab-pipelines.png)
- CI/CD流水线详情
  ![](http://img.iaquam.com/image/png/gitlab-pipelines-detail.png)
- 构建任务执行详情
  ![](http://img.iaquam.com/image/png/gitlab-job-build1.png)
  ![](http://img.iaquam.com/image/png/gitlab-job-build2.png)
- 测试任务执行详情
  ![](http://img.iaquam.com/image/png/gitlab-job-test.png)
- 部署任务执行详情
  ![](http://img.iaquam.com/image/png/gitlab-job-deploy1.png)
  ![](http://img.iaquam.com/image/png/gitlab-job-deploy2.png)
