---
title: git reset模式介绍
date: 2016-04-05 17:45:17
tags:
---

## 先看命令帮助的讲解

``` bash
Victors-MPB:hello wangsheng$ git reset -h
usage: git reset [--mixed | --soft | --hard | --merge | --keep] [-q] [<commit>]
   or: git reset [-q] <tree-ish> [--] <paths>...
   or: git reset --patch [<tree-ish>] [--] [<paths>...]

    -q, --quiet           be quiet, only report errors
    --mixed               reset HEAD and index
    --soft                reset only HEAD
    --hard                reset HEAD, index and working tree
    --merge               reset HEAD, index and working tree
    --keep                reset HEAD but keep local changes
    -p, --patch           select hunks interactively
    -N, --intent-to-add   record only the fact that removed paths will be added later
```

### 各种模式对比

|模式|HEAD是否重置|Index是否重置|working区是否重置|
|---|:-:|:-:|:-:|
|--mixed『缺省模式』|是|是|否|
|--soft|是|否|否|
|--hard|是|是|是|
|--merge|是|是|是|
|--keep|是|是|否|

### SourceTree 重置分析

#### 重置 -> 丢弃文件变更

- 操作截图

  ![git-reset-mixed-partial](http://7xsk2b.com2.z0.glb.clouddn.com/image/git-reset-mixed-partial.png)

- 实际执行的命令

``` bash
git -c diff.mnemonicprefix=false -c core.quotepath=false -c credential.helper=sourcetree reset -q HEAD -- a.txt c.txt 	
	
git -c diff.mnemonicprefix=false -c core.quotepath=false -c credential.helper=sourcetree checkout HEAD -- a.txt 
Completed successfully
```

- 结果

``` bash
Victors-MPB:hello wangsheng$ git st
On branch feature2
All conflicts fixed but you are still merging.
    (use "git commit" to conclude merge)

Untracked files:
    (use "git add <file>..." to include in what will be committed)

	  c.txt

nothing added to commit but untracked files present (use "git add" to track)
```

可以看出这样操作，git的上下文环境还在merge状态，这种情况一不小心就容易当做重置干净了，继续干自己的事情了，殊不知把别人的改动丢弃了。

#### 重置 -> 重置所有

- 操作截图

  ![git-reset-hard-all](http://7xsk2b.com2.z0.glb.clouddn.com/image/git-reset-hard-all.png)

- 实际执行的命令

``` bash
git -c diff.mnemonicprefix=false -c core.quotepath=false -c credential.helper=sourcetree reset -q --hard HEAD -- 

git -c diff.mnemonicprefix=false -c core.quotepath=false -c credential.helper=sourcetree submodule update --init --recursive 
Completed successfully
```

- 结果

``` bash
Victors-MPB:hello wangsheng$ git st
On branch feature2
nothing to commit, working directory clean
```
