---
title: Linux下MySQL数据库的备份和还原
date: 2018-05-08 18:54:25
categories: 数据库
tags: 
- Linux
- MySQL
- 备份
- 还原
---

对于生产的数据，做好定时备份是保证数据安全的必要措施。一旦出现数据丢失或者误删除，可用最近的一次备份，做数据还原。从而将数据的损失降低到最低。

## 常用的备份命令

### 1. 备份单个数据库

~~~Shell
mysqldump -hhost -uusername -ppassword dbname > /path_to_backup/backup_name.sql
~~~

备份并压缩

~~~Shell
mysqldump -hhost -uusername -ppassword dbname ｜ gzip > /path_to_backup/backup_name.sql.gz
~~~

### 2. 备份多个数据库

~~~Shell
mysqldump -hhost -uusername -ppassword databases dbname1 dbname2 dbname3 > /path_to_backup/backup_name.sql
~~~

### 3. 备份单个数据库里指定表的数据

~~~Shell
mysqldump -hhost -uusername -ppassword dbname table1 table2 table3 > /path_to_backup/backup_name.sql
~~~

### 4. 仅备份数据库的结构

~~~Shell
mysqldump -no-data -databases dbname > /path_to_backup/backup_name.sql
~~~

### 5. 备份所有的数据库

~~~Shell
mysqldump -all-databases > /path_to_backup/backup_name.sql
~~~

## 常用的还原命令

### 1. 使用无压缩的数据备份文件还原

~~~Shell
mysql －hhost -uusername -ppassword dbname < /path_to_backup/backup_name.sql
~~~

### 2. 使用已压缩的数据备份文件还原

~~~Shell
gunzip < /path_to_backup/backup_name.sql.gz | mysql -hhost -uusername -ppassword dbname
~~~

一键迁移到新数据库

~~~Shell
mysqldump －hhost -uusername -ppassword dbname | mysql -hnew_host -uusername -ppassword -C dbname
~~~

## 定时备份

以上的操作完全是手工操作，在实际生产环境不现实。因此需要做成定时自动备份的，这时就可以借助于Linux上的Crontab来实现。

### 1. 创建备份脚本

~~~Shell
#!/bin/sh

# This is a mysql database backup shell script for aquarium database.

# set mysql info
hostname="localhost"
username="xxx"
password="xxx"
database="xxx"

# set backup info
backup_path="/path/to/backup"
date=$(date +%Y%m%d_%H%M%S)

# execute backup
if [ ! -d $backup_path ]; then
  mkdir -p $backup_path
fi
mysqldump -h$hostname -u$username -p$password $database | gzip > $backup_path/$database_$date.sql.gz
~~~

### 2. 创建定时任务

键入 `crontab -e` 进入编辑模式，加入下面一行内容:

~~~Shell
# 添加定时计划，例如：每天凌晨2点执行
0 2 * * * /path/to/backup.sh
~~~

`:wq` 保存退出。

至此，自动定时备份已经完成。