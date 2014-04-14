---
layout: post
title: "让Appstore开启Degub菜单"
date: 2014-04-14 12:42:37 +0800
comments: true
categories: 
keywords: 
description: 
---

有时候在Mac App Store下载或更新应用的，会出现一些错误，造成这原因可能是由于cookie造成的。所以我们可以重设appstore的cookie。方法如下：<br>
1.打开Terminal, 运行:

```
defaults write com.apple.appstore ShowDebugMenu -bool true
```
2.重启Mac App Store， 大家就可以发现菜单栏多了一个Debug菜单。<br>
快选择清除cookie试试。

![](http://iosman.qiniudn.com/2014-04-14PM.png)
