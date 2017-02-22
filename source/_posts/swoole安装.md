---
title: Swoole安装
date: 2017-02-22 16:01:21
categories: PHP
tags:
- Swoole
- PHP
---

[Swoole](http://www.swoole.com/)是PHP的异步、并行、高性能网络通信引擎，使用纯C语言编写，提供了PHP语言的异步多线程服务器，异步TCP/UDP网络客户端，异步MySQL，异步Redis，数据库连接池，AsyncTask，消息队列，毫秒定时器，异步文件读写，异步DNS查询。 Swoole内置了Http/WebSocket服务器端/客户端、Http2.0服务器端。

除了异步IO的支持之外，Swoole为PHP多进程的模式设计了多个并发数据结构和IPC通信机制，可以大大简化多进程并发编程的工作。其中包括了并发原子计数器，并发HashTable，Channel，Lock，进程间通信IPC等丰富的功能特性。

Swoole2.0支持了类似Go语言的协程，可以使用完全同步的代码实现异步程序。PHP代码无需额外增加任何关键词，底层自动进行协程调度，实现异步。

Swoole可以广泛应用于互联网、移动通信、企业软件、云计算、网络游戏、物联网（IOT）、车联网、智能家居等领域。 使用PHP+Swoole作为网络通信框架，可以使企业IT研发团队的效率大大提升，更加专注于开发创新产品。

## 安装

访问 [Swoole Downloads](https://github.com/swoole/swoole-src/releases)下载源码，解压。

~~~Shell
$ cd /path/to/swoole_source_code
$ phpize
$ ./configure
$ make
$ sudo make install
~~~

>swoole的./configure有很多额外参数,可以通过./configure --help命令查看,这里均选择默认项

安装成功后，需要修改php.ini文件，在php.ini文件中添加swoole配置，配置如下：

~~~Shell
extension=swoole.so
~~~

随后在终端中输入命令 php -m 查看扩展安装情况。如果在列出的扩展中看到了swoole, 则说明安装成功。

## Hello World通讯演示

### 服务端

创建一个 Server.php 文件并输入如下内容:

~~~PHP
<?php
// Server
class Server
{
    private $serv;
    public function __construct() {
        $this->serv = new swoole_server("0.0.0.0", 9501);
        $this->serv->set(array(
            'worker_num' => 8,
            'daemonize' => false,
        ));
        $this->serv->on('Start', array($this, 'onStart'));
        $this->serv->on('Connect', array($this, 'onConnect'));
        $this->serv->on('Receive', array($this, 'onReceive'));
        $this->serv->on('Close', array($this, 'onClose'));
        $this->serv->start();
    }

    public function onStart( $serv ) {
        echo "Start\n";
    }

    public function onConnect( $serv, $fd, $from_id ) {
        $serv->send( $fd, "Hello {$fd}!" );
    }

    public function onReceive( swoole_server $serv, $fd, $from_id, $data ) {
        echo "Get Message From Client {$fd}:{$data}\n";
        $serv->send($fd, $data);
    }
    public function onClose( $serv, $fd, $from_id ) {
        echo "Client {$fd} close connection\n";
    }
}
// 启动服务器
$server = new Server();
~~~

### 客户端

创建一个 Client.php 文件并输入如下内容:

~~~PHP
<?php
class Client
{
    private $client;
    public function __construct() {
        $this->client = new swoole_client(SWOOLE_SOCK_TCP);
    }
    public function connect() {
        if( !$this->client->connect("127.0.0.1", 9501 , 1) ) {
            echo "Error: {$this->client->errMsg}[{$this->client->errCode}]\n";
        }
        fwrite(STDOUT, "请输入消息:"); $msg = trim(fgets(STDIN)); $this->client->send( $msg );
        $message = $this->client->recv();
        echo "Get Message From Server:{$message}\n";
    }
}
$client = new Client();
$client->connect();
~~~

### 运行

在Terminal下执行命令 php Server.php 即可启动服务器,在另一个Terminal下执行 php
Client.php ,输入要发送的内容,即可发送消息到服务器,并收到来自服务器的消息。