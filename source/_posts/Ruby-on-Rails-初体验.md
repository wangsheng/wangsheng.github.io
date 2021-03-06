---
title: Ruby on Rails 初体验
date: 2015-11-30 18:10:14
categories: Ruby
tags:
- Ruby
- Rails
---

使用命令行快速创建应用的步骤

- 创建应用程序

  ```Shell
  $rails new hello -d mysql
  ```
  **备注** 这里演示使用mysql作为数据持久层
- 浏览hello world界面

  ```Shell
  $cd hello
  $rake db:create RAILS_ENV='development' // 创建开发数据库
  $bin/rails server
  ```
  **备注** 由于这里的rails版本是4.2.1，与msqyl2的最高版本有不兼容行，所以需要手动将Gemfile中mysql2降级。降级之后执行bundle install。
  ```
  gem 'mysql2' -> gem 'mysql2', '~> 0.3.18'
  ```
- 使用脚手架，创建模型、数据迁移脚本以及控制器和试图

  ```Shell
  $bin/rails generate scaffold Person name:string age:integer
  ```
- 执行数据迁移

  ```Shell
  $bin/rake db:migrate RAILS_ENV=development
  ```
- 启动应用

  ```Shell
  $bin/rails server
  ```
- 浏览效果

  ```
  http://localhost:3000/people
  ```

**备注**

此演示项目，ruby、gems和Rails的版本信息如下:

- ruby: 2.0.0p481
- rubygems: 2.4.5
- rails: 4.2.1

参考：

- [rubyonrails.org](http://guides.rubyonrails.org/command_line.html)
