---
title: MySQL数据库导出和导入
date: 2017-01-17 13:21:21
categories: 数据库
tags:
- MySQL
---

## 导出

- 导出数据和表结构

~~~Shell
$ mysqldump -u[usermae] -p [dbname] > [dbname].sql
~~~

- 只导出表结构

~~~Shell
$ mysqldump -u[usermae] -p -d [dbname] > [dbname].sql
~~~

## 导入

- 创建一个空数据库

~~~Shell
mysql> create database [dbname] character set utf8;
~~~

- 创建MySQL用户并授权

~~~Shell
CREATE USER '[username]'@'localhost' IDENTIFIED BY '[password]';
GRANT ALL PRIVILEGES ON [dbname].* TO '[username]'@'localhost';
~~~

- 导入数据表和数据

~~~Shell
$ mysql -u[username] -p [dbname] < /path/to/[dbname].sql
~~~
