---
layout: post
title: "Mac iOS推送测试"
date: 2015-09-21 12:37:32 +0800
comments: true
categories: [iOS]
keywords: [iOS, Push Notification, 推送]
description: 
---


今天给大家介绍一个自己写的iOS推送测试程序，在Mac下有一个同类产品PushMeBaby,它使用的是.cer证书。一般我们都使用p12文件来保存证书与密钥，所以我写了一个用p12文件来测试推送的App,命名为EasyPush. [源码在此](https://github.com/ondev/EasyPush)，感兴趣的朋友可以拿来看看。


使用到的第三方库有:[CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket)

库依赖管理:[Carthage](https://github.com/Carthage/Carthage)
