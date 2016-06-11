---
title: 商户端APP集成微信扫描支付
date: 2015-07-10 14:11:10
toc: true
categories: Android
tags:
- 移动支付
---

## 准备工作

* APP端微信支付SDK
  [[Android]](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317784&token=&lang=zh_CN) | [[iOS]](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317782&token=&lang=zh_CN)
* 服务端微信支付SDK
  [[JAVA]](https://pay.weixin.qq.com/wiki/doc/api/download/wxpay_scanpay_java_sdk_proj-master.zip) |
  [[PHP]](https://pay.weixin.qq.com/wiki/doc/api/download/WxpayAPI_php_v3.zip)
* 服务端暴露notify接口
* push通道

## 支付流程

1. 用户在APP端浏览商品
2. 加入购物车
3. 结算
4. 调用提交订单接口
5. 提交订单接口返回预支付链接(**预支付链接可根据服务端微信支付sdk生成**)
6. APP根据链接生成二维码
7. 用户拿起手机扫描支付
8. 微信调用第三方开发者服务端暴露的notify接口
9. 第三方服务端在收到notify接口调用时，更新订单库里订单状态，同时调用push通道告诉APP支付成功
10. APP收到push命令后进行页面跳转
