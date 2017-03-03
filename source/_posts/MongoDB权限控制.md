---
title: MongoDB权限控制
date: 2017-02-19 17:26:40
categories: 数据库
tags:
- MongoDB
- NoSQL
---

上一篇 [MongoDB初识](http://victor87.coding.me/2017/02/16/MongoDB%E5%88%9D%E8%AF%86/) 已经介绍了MongoDB的基本概念和操作，聪明的同学可以已经发现，链接MongoDB怎么没有权限认证呢？那如果知道了ip和端口，就可以肆意操作了。其实MongoDB也有类似于MySQL数据的用户权限认证。这篇文章介绍如何开启MongoDB的权限认证。

## 创建访问MongoDB用户

~~~Shell
> db.createUser({user: "username", pwd: "password", roles: [{role:"readWrite", db:"dbname"}]});
Successfully added user: {
    "user" : "username",
    "roles" : [{
        "role" : "readWrite",
        "db" : "dbname"
    }]
}
~~~

## 开启权限认证

~~~Shell
$ echo "auth=true" >> /path/to/mongodb.conf
~~~

重启MongoDB服务

~~~Shell
$ mongod --config=/path/to/mongodb.conf
~~~

### 后台运行MongoDB

~~~Shell
$ mongod --config=/path/to/mongodb.conf --fork --logpath=/path/to/logfile
~~~

**注意:** `--fork`后台运行时，必须制定日志文件路径

## mongo shell客户端连接

~~~Shell
$ mongo
>use dbname;
>db.auth('username', 'password');
1
~~~

至此，权限认证开启和使用已经介绍完毕，更多关于用户角色，可看下面：

## 链接远程MongoDB

~~~Shell
$mongo --host=IP --port=27017
~~~

## MongoDB数据库角色

1. 内建的角色
1. 数据库用户角色：read、readWrite;
1. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
1. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
1. 备份恢复角色：backup、restore；
1. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
1. 超级用户角色：root // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
1. 内部角色：__system

> 角色说明：
> Read：允许用户读取指定数据库
> readWrite：允许用户读写指定数据库
> dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
> userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
> clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
> readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
> readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
> userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
> dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
> root：只在admin数据库中可用。超级账号，超级权限

参考 [如何对MongoDB 3.2.7进行用户权限管理配置](http://www.jianshu.com/p/a4e94bb8a052)
