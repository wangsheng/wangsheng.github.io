---
title: R语言初识
date: 2017-09-15 09:01:27
categories: 大数据 & AI
tags:
- R
- 数据分析
- 科学计算
---

[R语言](https://www.r-project.org)，主要用于统计分析、绘图、数据挖掘。R本来是由来自新西兰奥克兰大学的罗斯·伊哈卡和罗伯特·杰特曼开发（也因此称为R），现在由“R开发核心团队”负责开发。R基于S语言的一个GNU计划项目，所以也可以当作S语言的一种实现，通常用S语言编写的代码都可以不作修改的在R环境下运行。R的语法是来自Scheme。

R的源代码可自由下载使用，亦有已编译的可执行文件版本可以下载，可在多种平台下运行，包括UNIX（也包括FreeBSD和Linux）、Windows和MacOS。R主要是以命令行操作，同时有人开发了几种图形用户界面。[RStudio](https://www.rstudio.com) 是一款功能强大的一个R语言开发IDE，在安装好R的官方版本后安装RStudio可以更方便地使用R。

## R语言安装

访问[下载页面](https://mirrors.tuna.tsinghua.edu.cn/CRAN/)，选择你操作系统对应的发行版，下载安装即可。

## RStudio安装

访问[RStudio官网](https://www.rstudio.com/)，下载对应的发型版本安装即可。

![RStudio](http://img.iaquam.com/image/RStudio.png)

## R简单使用

### 四则运算

~~~R
> 1 + (5 - 4)*10/2.5 + 1.1E3
[1] 1105
~~~

> 1.1E3是科学记数法， 表示1.1×10的3次方

### 数学函数

#### 平方根、指数、对数

~~~R
> x <- 9
> sqrt(x)
[1] 3
> exp(1)
[1] 2.718282
> log10(10000)
[1] 4
~~~

> sqrt(9)表示对9开平方根，结果为3。 exp(1)表示e的1次方，结果为e=2.718282。 log10(10000)表示lg10000，结果为4。 log为自然对数。

#### 取整

~~~R
> round(1.114, 2)
[1] 1.11
> round(1.115, 2)
[1] 1.12
> round(-1.115, 2)
[1] -1.12
> floor(1.4)
[1] 1
> floor(1.5)
[1] 1
> floor(-1.5)
[1] -2
> ceiling(1.4)
[1] 2
> ceiling(1.5)
[1] 2
> ceiling(-1.5)
[1] -1
~~~

> round四舍五入运算。floor向下取整。ceiling向上取整

#### 三角函数

~~~R
> pi
[1] 3.141593
> sin(pi/6)
[1] 0.5
> cos(pi/3)
[1] 0.5
> tan(pi/4)
[1] 1
~~~

#### 统计函数

~~~R
> x <- c(1, 2, 3, 4, 5, 6, 7)
> sum(x)
[1] 28
> mean(x)
[1] 4
> var(x)
[1] 4.666667
> sd(x)
[1] 2.160247
> min(x)
[1] 1
> max(x)
[1] 7
> range(x)
[1] 1 7
~~~

> sum(求和), mean(平均值), var(样本方差), sd(样本标准差), min(最小值), max(最大值), range(最小值和最大值)

### 图形工具

#### 条形图示例

~~~R
> barplot(c('男生'=10, '女生'=7), main='男女生人数', family='宋体')
~~~

![barplot](http://img.iaquam.com/image/barplot.png)

#### 散点图示例

~~~R
> plot(1:10, sqrt(1:10))
~~~

![plot](http://img.iaquam.com/image/plot.png)

### 分析表格数据

~~~R
travel <- read.csv("~/path/to/travel.csv", header=TRUE, as.is=TRUE)
> table(travel[,'购票方式'])

代售点 飞机场 火车站 汽车站   网上 
10      1      2      2    15
> table(travel[,'交通工具'])

飞机 火车 汽车 
 6   20    4
> table(travel[,'购票方式'], travel[,'交通工具'])
        
         飞机 火车 汽车
  代售点    1    8    1
  飞机场    1    0    0
  火车站    0    2    0
  汽车站    0    0    2
  网上      4   10    1
> knitr::kable(table(travel[,'购票方式'], travel[,'交通工具']))
Error in loadNamespace(name) : 不存在叫‘knitr’这个名字的程辑包
> install.packages("knitr") //如果出现以上错误，需要安装knitr这个包
.....
> knitr::kable(table(travel[,'购票方式'], travel[,'交通工具'])) //安装好后，重新执行
|       | 飞机| 火车| 汽车|
|:------|----:|----:|----:|
|代售点 |    1|    8|    1|
|飞机场 |    1|    0|    0|
|火车站 |    0|    2|    0|
|汽车站 |    0|    0|    2|
|网上   |    4|   10|    1|
~~~

> 示例中用到的csv文件，下载地址 [travel.csv](http://img.iaquam.com//file/travel.csv)

[更多R语言教程](http://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/index.html)