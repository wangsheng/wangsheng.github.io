---
title: Git冲突解决方案
date: 2019-01-15 18:17:12
categories: Git
tags: 
- git
---

## 背景

假设多人协作是基于dev开发，每个人开发时，从dev拉取新的分支进行开发。

## 冲突场景

假设work1从dev拉取了分支branch_work1进行开发；work2从dev拉取了分支branch_work2进行开发。

而work1先完成了代码提交，并合并到了dev上。

那么当work2完成工作后往dev合并时，如果他们两个人没有同时修改一个文件，那么work2可以顺利合并到dev上。

但是如果他们两个修改了同一个文件，这时work2进行合并到dev时，会提示『There are merge conflicts.』。这时，work2是需要先合并且手动解决冲突后，方可合并到dev上。

**注意：**
> 执行冲突合并操作时，务必保证本地代码都已提交。不要没提交代码，就来执行merge/pull或者rebase操作。

## 解决冲突方法

### 方案一：merge

如果branch_work2分支有多个人参与开发，那么采用此方法

~~~Shell
$ git pull origin dev // 前提是当前在branch_work2分支上
手动解决冲突...
$ git add conflit_file
$ gie merge --continue
$ esc :wq 保存退出
$ git push origin branch_work2 // 推到远程，用于Code Review和合并
~~~

### 方案二: rebase

如果branch_work2分支只有你一个人参与开发，那么采用此方法

~~~Shell
$ git pull --rebase origin dev // 前提是当前在branch_work2分支上
手动解决冲突...
$ git add conflit_file
$ git rebase --continue
$ git push -f origin branch_work2 // 强制推到远程，用于Code Review和合并
~~~

### merge 和 rebase 方案对比

- merge 方案不会改变历史，因此解决冲突后推送到远程时不需要加-f强制推送。但是提交历史会多一次合并提交。
  ![](http://img.iaquam.com/image/png/merge.png)
- rebase 方案尽管让提交树看起来很线性，没有多产生一次合并提交。但是他改变了历史，因此解决冲突后需要加-f强制推送。也正是这个原因，当别人也参与_work2分支开发时，不应该选用此方案，避免给其他同事带来伤害。
  ![](http://img.iaquam.com/image/png/rebase.png)