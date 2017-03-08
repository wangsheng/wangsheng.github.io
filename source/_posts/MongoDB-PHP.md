---
title: MongoDB PHP
date: 2017-02-19 17:26:21
categories: 数据库
tags:
- MongoDB
- NoSQL
- PHP
---

上一篇 [MongoDB初识](http://victor87.coding.me/2017/02/16/MongoDB%E5%88%9D%E8%AF%86/) 已经介绍了MongoDB的基本概念和操作，那么如何与PHP集成使用呢？用过MySQL的就知道，需要一个mysql-driver驱动。同理，在PHP中与MongoDB通讯，也需要一个mongodb-driver。下面介绍PHP如何集成MongoDB。

## 安装PHP MongoDB驱动

- 最新源码下载地址

访问 [MongoDB PHP Driver](https://github.com/mongodb/mongo-php-driver), 下载源码。

- 稳定官方下载地址

访问 [https://pecl.php.net/package/mongodb](https://pecl.php.net/package/mongodb), 下载源码。

不喜欢当小白鼠的，请选择稳定的官方地址

~~~Shell
$ tar zxvf mongodb-mongodb-php-driver-<commit_id>.tar.gz
$ cd mongodb-mongodb-php-driver-<commit_id>
$ phpize
$ ./configure
$ sudo make install
~~~

注意，如果你是采用git clone的方式下载MongoDB PHP Driver, 则需要执行子模块clone下载。因为，这个驱动版本库用到了git的submodule.

~~~Shell
git submodule update --init
~~~

安装成功后，需要修改php.ini文件，在php.ini文件中添加mongodb配置，配置如下：

~~~Shell
extension=mongodb.so
~~~

## PHP7 中操作MongoDB

### 插入数据

将title为"Hello", content为"Hello world!"的消息数据插入到 test 数据库的 message 集合中。

~~~PHP
<?php
$bulk = new MongoDB\Driver\BulkWrite;
$document = ['_id' => new MongoDB\BSON\ObjectID, 'title' => 'Hello', 'content' => 'Hello world!'];

$bulk->insert($document);

$manager = new MongoDB\Driver\Manager("mongodb://localhost:27017");
$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $manager->executeBulkWrite('test.message', $bulk, $writeConcern);
?>
~~~

### 查询

查询test数据库中message集合中的数据

~~~PHP
<?php

// 创建MongoDB的驱动管理对象
$manager = new MongoDB\Driver\Manager("mongodb://localhost:27017");

// 查询数据
$filter = [];
$options = [];
$query = new MongoDB\Driver\Query($filter, $options);
$cursor = $manager->executeQuery('test.message', $query);

foreach ($cursor as $document) {
    print_r($document);
}

?>
~~~

结果

~~~PHP
stdClass Object
(
    [_id] => MongoDB\BSON\ObjectID Object
        (
            [oid] => 58a96ed3d6da0366bd7ba081
        )

    [title] => Hello
    [content] => Hello world!
)
~~~

### 更新数据

将test数据库中message集合中title为'Hello'的记录content改为"你好，世界!"

~~~PHP
<?php
$bulk = new MongoDB\Driver\BulkWrite;
$bulk->update(['title' => 'Hello'], ['$set' => ['content' => '你好，世界!']]);
$manager = new MongoDB\Driver\Manager("mongodb://localhost:27017");
$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $manager->executeBulkWrite('test.message', $bulk, $writeConcern);
?>
~~~

结果

~~~Shell
> db.message.find().pretty()
{
	"_id" : ObjectId("58a96ed3d6da0366bd7ba081"),
	"title" : "Hello",
	"content" : "你好，世界!"
}
~~~

## 删除

删除test数据库中message集合中title为'Hello'的记录

~~~PHP
<?php
$bulk = new MongoDB\Driver\BulkWrite;
$bulk->delete(['title' => 'Hello'], ['limit' => 1]);   // limit 为 1 时，删除第一条匹配数据; limit 为 0 时，删除所有匹配数据

$manager = new MongoDB\Driver\Manager("mongodb://localhost:27017");
$writeConcern = new MongoDB\Driver\WriteConcern(MongoDB\Driver\WriteConcern::MAJORITY, 1000);
$result = $manager->executeBulkWrite('test.message', $bulk, $writeConcern);
?>
~~~

结果

~~~Shell
> db.message.find().pretty()
>
~~~

## YII2中使用MongoDB

### 安装MongoDB PHP Driver, 见上面
### 添加yii2-mongodb模块

composer.json中添加如下声明

~~~
"yiisoft/yii2-mongodb": "~2.1.0"
~~~

### MongoDB链接配置

~~~
return [
    //....
    'components' => [
        'mongodb' => [
            'class' => '\yii\mongodb\Connection',
            'dsn' => 'mongodb://user:password@localhost:27017/mydatabase',
        ],
    ],
];
~~~

### 操作

- model创建

~~~PHP
use yii\mongodb\ActiveRecord;

class Message extends ActiveRecord
{

    public static function insertData($title, $content)
    {
        $message = new Message();
        $message->title = $title;
        $message->content = $content;
        return $message->save();
    }

    public function attributes()
    {
        return ['_id', 'title', 'content'];
    }
}
~~~

- controller中添加action

~~~PHP
......
public function hello()
{
    Message::insertData('Hello', "Hello world from yii2");
}
......
~~~

- 执行结果

~~~Shell
> db.message.find().pretty()
{
	"_id" : ObjectId("58a98435d6da036e88287a04"),
	"title" : "Hello",
	"content" : "Hello world from yii2"
}
~~~