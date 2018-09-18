---
title: MySQL支持Emoji表情
date: 2017-02-01 16:43:26
categories: 数据库
tags:
- MySQL
---

随着移动端的兴起，越来越多的应用用户产生的内容需要支持Emoji表情，来增加趣味性。默认情况下，MySQL是不能直接插入Emoji表情的，当应用程序直接传递带表情的内容，随后就看到非法字符之类的异常了。那么，如何才能保存带有Emoji表情的内容呢？答案是将utf8编码换成utf8mb4。总共牵扯到的地方有以下：

## 修改MySQL的配置文件`my.cnf`

添加以下内容到 /path/to/my.cnf
~~~
[client]
# 客户端来源数据的默认字符集
default-character-set = utf8mb4

[mysqld]
# 服务端默认字符集
character-set-server=utf8mb4

# 连接层默认字符集
collation-server=utf8mb4_unicode_ci

[mysql]
# 数据库默认字符集
default-character-sert = utf8mb4
~~~

## 应用中用到的数据库指定编码集为`utf8mb4`

~~~
create database <db_name> character set utf8mb4;
~~~

## 应用层数据库连接适配器指定编码集为`utf8mb4`

例如，yii2中

~~~PHP
'charset' => 'utf8mb4’,
~~~

## 如果应用中指定了表的编码集，那么也需要显示声明为`utf8mb4`

例如Yii的Migration中

~~~
$tableOptions = 'CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci ENGINE=InnoDB';
~~~

经过上面的设置，重启MySQL后，就能顺利实现插入带Emoji表情的内容了。附一个效果图:
![mysql-emoji-utf8mb4](http://img.iaquam.com/image/mysql-emoji-utf8mb4.png)

