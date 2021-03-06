---
title: redis
date: 2017-08-31 17:07:35
categories: 大数据 & AI
tags:
- Redis
- PHP
---

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。跟Memcached相比，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。更多关于Redis和Memcache的比较，可参见[memcached redis 对比分析](http://www.jianshu.com/p/e94fa7340923)。

## 安装Redis

### MAC 系统

~~~Shell
brew install redis
~~~

配置文件路径: `/usr/local/etc/redis.conf`

### Centos Linux

~~~Shell
yum install redis
~~~

配置文件路径: `/etc/redis.conf`

## 启动：

~~~Shell
redis-server
~~~

### 后台启动

- 编辑 redis.conf文件，修改daemonize为yes

~~~
daemonize yes
~~~

- 启动

~~~Shell
redis-server /path/to/redis.conf
~~~

## 检测redis是否启动成功

~~~Shell
Victors-MBP:~ wangsheng$ redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> INFO
# Server
redis_version:4.0.1
......
127.0.0.1:6379> set message "Hello World"
OK
127.0.0.1:6379> get message
"Hello World"
127.0.0.1:6379> exit
~~~

## 设置Redis远程访问以及权限控制

(1). 编辑配置文件，添加如下内容

~~~
bind 192.168.1.xxx
requirepass <password>
~~~

(2). 重启redis

~~~Shell
$ redis-server /path/to/redis.conf
~~~

### 客户端认证和连接

~~~Shell
$ redis-cli -h 192.168.1.xxx -a <password>
192.168.1.xxx:6379> ping
PONG
~~~

或者

~~~Shell
$ redis-cli -h 192.168.1.xxx
192.168.1.xxx:6379> ping
(error) NOAUTH Authentication required.
192.168.1.xxx:6379> auth <password>
OK
192.168.1.xxx:6379> ping
PONG
~~~

## 安装Redis的PHP扩展

### MAC 系统

~~~Shell
brew install php70-redis
~~~

配置文件路径: `/usr/local/etc/php/7.0/conf.d/ext-redis.ini`

### Centos Linux

~~~Shell
yum install php70w-pecl-redis
~~~

配置文件路径: `/etc/php.d/redis.ini`

## Redis 持久化配置

参见 [Redis持久化 Snapshot和AOF说明](https://www.cnblogs.com/zhoujinyi/archive/2013/05/26/3098508.html)

## Redis 单机多实例部署

参见 [Redis单机配置多实例，实现主从同步](https://www.cnblogs.com/lgeng/p/6623336.html)

~~~diff
$ diff /etc/redis.conf /etc/redis_6380.conf
84c84
< port 6379
---
> port 6380
150c150
< pidfile /var/run/redis_6379.pid
---
> pidfile /var/run/redis_6380.pid
163c163
< logfile /var/log/redis/redis.log
---
> logfile /var/log/redis/redis_6380.log
237c237
< dbfilename dump.rdb
---
> dbfilename dump_6380.rdb
597c597
< appendfilename "appendonly.aof"
---
> appendfilename "appendonly_6380.aof"
1052a1053
> :q
~~~

## Yii中配置Redis

(1). composer.json中添加`yii2-reds`中间件

~~~
"yiisoft/yii2-redis": "~2.0.0"
~~~

(2). 通过执行`composer update`安装

(3). 添加redis具体参数配置`common/config/main.php`

~~~
return [
    //....
    'components' => [
        'redis' => [
            'class' => 'yii\redis\Connection',
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 0,
        ],
    ]
];
~~~

## Yii中使用Redis

~~~PHP
// 设置字符串类型的key-value
$result_str = Yii::$app->redis->set('str', 'Hello Redis!');
dump($result_str); // true

// 获取str对应的value
dump(Yii::$app->redis->get('str')); // Hello Redis!

// 查看所有的key
dump(Yii::$app->redis->keys('*')); // [0 => "str"]

// 删除key
$result_del = Yii::$app->redis->del('str');
dump($result_del); // 1

// 列表
$result_list = Yii::$app->redis->lpush('list', 'list1'); //list里数组的长度:1
dump($result_list);
$result_range = Yii::$app->redis->lrange('list', 0, 2); // 取出下标为[0, 2]
dump($result_range);
$result_update = Yii::$app->redis->lset('list', 1,'list2');
dump($result_update); // true

// 哈希 适合存储对象
$result_set = Yii::$app->redis->hmset('person', 'name', 'victor', 'age', 30);
dump($result_set); //true
dump(Yii::$app->redis->hgetall('person')); // [0=>"name", 1=>"victor", 2=>"age", 3=>"30"]

// 集合
$result_set = Yii::$app->redis->sadd('set', 'a', 'b', 'c', 'b');
dump($result_set); // 3
dump(Yii::$app->redis->scard('set')); //3
dump(Yii::$app->redis->smembers('set')); //[0=>"b", 1=>"c", 2=>"a"]

// 有序集合
$result_zset = Yii::$app->redis->zadd('zset', 1, 'a', 2, 'b', 3, 'c');
dump($result_zset); //3
dump(Yii::$app->redis->zcard('zset')); ///3
dump(Yii::$app->redis->zrange('zset', 0, 1)); //[0=>"a", 1=>"b"]
~~~

