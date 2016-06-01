---
title: weui
date: 2015-12-21 13:40:43
tags:
---

**WeUI 为微信 Web 服务量身设计**

> WeUI是一套同微信原生视觉体验一致的基础样式库，由微信官方设计团队为微信 Web 开发量身设计，可以令用户的使用感知更加统一。包含button、cell、dialog、 progress、 toast、article、icon等各式元素。

查看演示实例

  ```Shell
  git clone https://github.com/weui/weui.git
  cd weui
  npm install -g gulp
  npm install
  gulp -ws
  ```
  运行gulp -ws命令，会监听src目录下所有文件的变更，并且默认会在8080端口启动服务器，然后在浏览器打开 http://localhost:8080/example。

参考 [github-weui](https://github.com/weui/weui)
