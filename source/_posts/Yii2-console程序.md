---
title: Yii2 console程序
date: 2017-02-22 16:52:56
categories: PHP
tags:
- Yii2
- PHP
---

Yii框架完美支持控制台应用，其在 Yii 中的结构与 Yii 的 web 应用非常相似。一个控制台应用，包括一个或多个 `[[yii\console\Controller]]`（通常在控制台环境中被称作“命令”）。 如 Web Controller 一样，每一个 `[[yii\console\Controller]]` 也可以包含有一个或多少的动作`action`。

## 用法

使用下面的语法来执行控制台 `Controller` 中的动作：

~~~Shell
yii <route> [--option1=value1 --option2=value2 ... argument1 argument2 ...]
</route>
~~~

例如， [[yii\console\controllers\HelloController::actionSay($name="Bob")|HelloController::actionSay($name="Bob")]] 中的 `say` 动作

~~~PHP
<?php

namespace console\controllers;


use yii\console\Controller;

class HelloController extends Controller
{
    public function actionSay($name="Bob")
    {
        dump('Hi, ' . $name);
        return 0;
    }
}
~~~

~~~Shell
yii hello/say victor
~~~

执行结果:

~~~Shell
Victors-MBP:thor-web wangsheng$ ./yii hello/say victor
"Hi, victor"
~~~

## 入口脚本

Yii Web应用入口脚本是`index.php`，而控制台应用入口脚本是`yii`， 它通常被放置于应用的根目录中。 控制台应用入口脚本所包含的代码主体如下：

~~~PHP
#!/usr/bin/env php
<?php

defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

require(__DIR__ . '/vendor/autoload.php');
require(__DIR__ . '/vendor/yiisoft/yii2/Yii.php');
require(__DIR__ . '/common/config/bootstrap.php');
require(__DIR__ . '/console/config/bootstrap.php');

$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/common/config/main.php'),
    require(__DIR__ . '/common/config/main-local.php'),
    require(__DIR__ . '/console/config/main.php'),
    require(__DIR__ . '/console/config/main-local.php')
);

$application = new yii\console\Application($config);
$exitCode = $application->run();
exit($exitCode);
~~~

这个脚本会被创建为你应用程序的一部分；你可以随意调整来满足你的需求。如果你不想在发生错误时看到一大段堆栈跟踪报错或者只是想单纯地提高总体性能，请将`YII_DEBUG`常量设置为`false`。 为了能提供更友好的开发环境，在`basic`和`advanced`两个应用程序模板中，`debug`选项默认是开启的。

## 配置

从上面的代码中可以看出，控制台应用程序的配置文件跟web程序一样，有common里公用的配置文件，以及独立的main<-local>.php。根据需要，自己放置配置项。

有时候，你可能希望运行一个跟入口脚本中指定的不一样的配置文件的控制台命令。 你只需简单地通过 appconfig 选项，在执行命令时，制定一个自定义的配置文件即可:

~~~ Shell
yii <route> --appconfig=path/to/config.php ...
~~~

## 创建自己的命令程序

参见上面示例的HelloController::actionSay($name="Bob")

## 退出

使用退出代码是控制台应用程序开发的最佳实践。通常，一个命令返回`0`说明一切都OK。如果它是一个大于零的数，可以理解为存在一个错误。返回的数值会是错误代码，它可能在发现错误详情方面会有用。 例如，`1` 一般可以代表一个未知的错误，用其他大于1的代码来代指一些特定的错误：输入错误，文件丢失等等。

为了使你的控制台命令返回一个退出代码，你只需在控制器动作中返回一个整数：

~~~PHP
public function actionIndex()
{
    if (/* 一些问题 */) {
        echo "发生一个问题!\n";
        return 1;
    }
    // 做一些事情
    return 0;
}
~~~

参考: [tutorial-console](http://www.yiifans.com/yii2/guide/tutorial-console.html)