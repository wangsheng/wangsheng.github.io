---
title: Spring Boot入门
date: 2018-09-22 09:07:23
categories: Java
tags:
- 微服务
- Spring Boot
---

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架本质是通过整合现有Spring的库，提供不同场景的集成工件包，从而简化新建项目时繁杂的配置以及难以搞定依赖包版本兼容性问题。

- spring-boot-starter: 最基础的工件集，每个Spring Boot项目必备
- spring-boot-starter-test: 测试工件集，每个Spring Boot项目初始化默认配备
- spring-boot-starter-web: web开发工件集
- spring-boot-devtools: 开发辅助工件集，比方说热启动开发模式。
- spring-boot-starter-data-jpa, mysql-connector-java 数据持久层开发工件集(这里以MySQL为例)

## 创建一个Spring Boot项目

### 1. 打开IDE -> Create New Project

  ![创建项目1](http://img.iaquam.com/image/jpgSpringBootChapter1_1.png)

  ![创建项目2](http://img.iaquam.com/image/jpgSpringBootChapter1_2.png)

  ![创建项目3](http://img.iaquam.com/image/jpgSpringBootChapter1_3.png)

  ![创建项目4](http://img.iaquam.com/image/jpgSpringBootChapter1_4.png)

### 2. 项目结构

  ![项目结构](http://img.iaquam.com/image/jpgSpringBootChapter1_5.png)

Spring Boot的基础结构共三个文件:

- src/main/java  程序开发以及主程序入口
- src/main/resources 配置文件
- src/test/java  测试程序

## 编写一个HelloWorld程序

### 1. 添加Web开发工件集

~~~XML
<!-- 引入Web开发工件集-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
~~~

### 2. 创建控制器

~~~Java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by wangsheng on 2018/9/22.
 */

@RestController
public class HelloController {

    @RequestMapping("/")
    public String index() {
        return "Index";
    }

    @RequestMapping("/hello")
    public String hello() {
        return "Hello World!";
    }
}
~~~

### 3. 启动应用，浏览器查看

- 访问http://localhost:8080/

  ![访问项目根路径](http://img.iaquam.com/image/jpgSpringBootChapter1_6.png)

- 访问http://localhost:8080/hello

  ![访问/hello路径](http://img.iaquam.com/image/jpgSpringBootChapter1_7.png)


