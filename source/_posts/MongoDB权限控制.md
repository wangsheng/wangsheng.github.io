---
title: MongoDB权限控制
date: 2017-02-19 17:26:40
categories: 数据库
tags:
- MongoDB
- NoSQL
---

上一篇 [MongoDB初识](http://victor87.coding.me/2017/02/16/MongoDB%E5%88%9D%E8%AF%86/) 已经介绍了MongoDB的基本概念和操作，聪明的同学可以已经发现，链接MongoDB怎么没有权限认证呢？那如果知道了ip和端口，就可以肆意操作了。其实MongoDB也有类似于MySQL数据的用户权限认证。这篇文章介绍如何开启MongoDB的权限认证。

MongoDB 通过基于角色的授权授予对数据和命令的访问权限，并提供内置角色来定义数据库的不同访问级别。另外，还提供了用户自定义的角色。

MongoDB为每个数据库提供了内置的数据库用户(database user)和数据库管理(database administration)角色。MongoDB仅为admin数据库提供了所有内置的其他角色。

## [Built-In Roles](https://docs.mongodb.com/manual/reference/built-in-roles/#built-in-roles)

### Database User Roles

每个数据库包含以下客户端角色：

- read
  提供读取所有非系统集合和以下系统集合上的数据功能：system.indexes, system.js和system.namespaces集合。
- readWrite
  在read的基础上，额外提供非系统集合和system.js集合上的数据功能。

### Database Administration Roles

每个数据库包含以下数据库管理角色：

- dbAdmin
  提供执行schema相关任务，索引和收集统计等管理任务的功能。此角色不授予用户和角色管理权限。
- userAdmin
  提供在当前数据库上创建和修改角色和用户的权限。由于`userAdmin`角色允许给任何用户(包括他们自己)授予任何角色，因此如果作用于`admin`数据库，该角色还间接提供了对数据库或者集群的超级用户访问权限。

- dbOwner
  数据库拥有者(owner)可以执行任何数据库管理的操作。该角色集成了 `readWrite`，`dbAdmin`， `userAdmin`角色授予的权限。

### Cluster Administration Roles

admin数据库不仅提供了单个数据库的管理角色，还提供了整个系统的管理角色。这些角色不限于副本集和分片集群管理功能。

- clusterAdmin
  提供最大的集权管理访问权限。此角色集 `clusterManager`, `clusterMonitor` 和 `hostManager` 权限于一体。此外，该角色还提供了 `dropDatabase` 操作。
- clusterManager
  提供集群上的管理和监控操作。此角色的用户可以访问位于分片和副本上的`config` 和 `local` 数据库。 
- clusterMonitor
  为监控工具（例如 [MongoDB Cloud Manager](https://www.mongodb.com/cloud/cloud-manager/?jmp=docs) 和 Ops Manager 监控代理程序）提供了只读权限。
- hostManager
  提供监控和管理服务的功能。

### Backup and Restoration Roles

admin数据库包含以下关于备份和还原数据的角色：

- backup
  提供用于备份数据需要的最小权限。该角色为 [MongoDB Cloud Manager](https://www.mongodb.com/cloud/cloud-manager/?jmp=docs) 备份代理或者使用`mongodump`备份整个`mongod`示例，提供了足够的权限。
- restore
  提供从备份文件(除了`system.profile`集合)恢复数据所需的权限。该角色为使用 `mongorestore`命令(不包含—oplogReplay选项)恢复数据提供了足够的权限。

### All-Database Roles

admin数据库提供了以下角色，适用于除了`local`和`config`之外所有数据库的权限：

- readAnyDatabase
  提供了作用于除了`local`和`config`外所有数据库，与`read`角色一样的权限。另外，该角色也提供了对于集群的`listDatabases`操作的权限。
- readWriteAnyDatabase
  提供了作用于除了`local`和`config`外所有数据库，与`readWrite`角色一样的权限。另外，该角色也提供了对于集群的`listDatabases`操作的权限。
- userAdminAnyDatabase
  提供了作用于除了`local`和`config`外所有数据库，与`userAdmin`角色一样管理操作的权限。同时也提供了对于集群的以下权限：
  - authSchemaUpgrade
  - invalidateUserCache
  - listDatabases
- dbAdminAnyDatabase
  提供了作用于除了`local`和`config`外所有数据库，与`dbAdmin`角色一样的权限。另外，该角色也提供了对于集群的`listDatabases`操作的权限。

### Superuser Roles

有几个角色提供了系统级别间接或者直接的超级用户访问权限。

以下角色提供了为任何用户授予数据库任意权限的能力，这意味着具有这些角色的任何一个用户都可以给他们自己授予任意数据库的任意权限：

- dbOwner 角色，当作用于admin数据库
- userAdmin 角色，当作用于admin数据库
- userAdminAnyDatabase 角色

以下角色拥有所有资源的所有权限：

- root

  提供对readWriteAnyDatabase, dbAdminAnyDatabase, userAdminAnyDatabase, clusterAdmin, restore和backup组合操作和所有资源的访问。

### Internal Role

- _ _system
  MongoDB将此角色分配给标识集群成员的用户对象，例如副本集成员或mongos示例。该角色使其拥有者有权对数据库中的任何对象采取任何操作。

  除特殊情况外，请勿将此角色分配给表示应用程序或人员管理员的用户对象。

  如果需要访问所有资源上的所有操作(例如运行applyOps命令)，请不要分配此角色。相反，创建一个自定义的用户角色，在anyResource上授予anyAction，并确保只有需要访问这些操作的用户才具有此访问权限。

## 配置Mongo权限认证

MongoDB默认没有管理员账号，因此需要先创建管理员账号，再开启权限认证。

mongoDB自带的数据库admin中，含有system.users集合，该集合用来存储用户认证以及授权信息。该集合的Schema如下:

~~~JavaScript
{
  _id: <system defined id>,
  user: "<name>",
  db: "<database>",
  credentials: { <authentication credentials> },
  roles: [
           { role: "<role name>", db: "<database>" },
           ...
         ],
  customData: <custom information>
}
~~~

字段解析：

- _id
  主键ID
- user
  用户名字符串。每个用户存在于单个逻辑数据库上下文，但可以通过roles指令授予其他数据库的访问权限。
- db
  指定用户关联的数据库。用户的权限不一定限制在此数据库。可以通过roles指令授予其他数据库的访问权限。**此数据决定用户auth时应该切换到的数据库。**
- credentials
  用户认证信息字段。
- roles
  roles数组可以包含多条给用户授权的role文档。
  - roles.role
    role的名字，包含内置 role 和用户自定义 role。
  - roles.db
    定义角色的数据库的名称。
- customData
  该字段包含可选的用户自定义信息

### 1. 创建访问MongoDB用户

创建系统管理员

~~~Shell
> use admin
switched to db admin
> db.createUser({user: "root", pwd: "password4root", roles: [{role:"root", db:"admin"}]})
Successfully added user: {
	"user" : "root",
	"roles" : [
		{
			"role" : "root",
			"db" : "admin"
		}
	]
}
~~~

创建某一应用数据库用户

~~~Shell
> use app
switched to db app
> db.createUser({user: "zhangsan", pwd: "password4zhangsan", roles: [{role:"readWrite", db:"app"}]})
Successfully added user: {
    "user" : "zhangsan",
    "roles" : [{
        "role" : "readWrite",
        "db" : "app"
    }]
}
~~~

> db.dropUser('userName') // 删除指定用户名的用户

### 2. 开启权限认证

~~~Shell
$ echo "auth=true" >> /path/to/mongodb.conf
~~~

重启MongoDB服务

~~~Shell
$ mongod --config=/path/to/mongodb.conf
~~~

#### 后台运行MongoDB

~~~Shell
$ mongod --config=/path/to/mongodb.conf --fork --logpath=/path/to/logfile
~~~

**注意:** `--fork`后台运行时，必须制定日志文件路径

#### 开启IPv6

~~~Shell
$ mongod --config=/path/to/mongodb.conf --fork --logpath=/path/to/logfile --filePermissions 0777 --ipv6
~~~

### 3. 验证权限认证

#### mongo shell客户端连接

~~~Shell
$ mongo
>use app;
>db.auth('zhangsan', 'password4zhangsan');
1
~~~

#### 连接远程MongoDB

~~~Shell
$mongo --host=IP --port=27017
~~~
