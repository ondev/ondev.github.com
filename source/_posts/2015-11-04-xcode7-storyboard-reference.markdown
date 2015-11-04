---
layout: post
title: "Xcode7 Storyboard Reference"
date: 2015-11-04 15:15:17 +0800
comments: true
categories: [Xcode7, iOS9]
keywords: [Xcode7, iOS9]
description: Interface Builder改进
---

Storyboard在团队开发中一直被人鄙弃，因为其Merge的时候很容易出现冲突。在Xcode7中Storyboard Reference的引入，这个问题将得到解决。


接下来我们就来看看它能做什么。

用Xcode7创建一个tabbar的工程。

1. 分离storyboard

在Main.storyboard中选中要分离的UIViewController, 然后顺序点菜单：Editor->Refactor to Storyboard, 这样就可以将选中的UIViewController分离到一个新的storyboard中去。而在Main.storyboard中被分离出来的UIViewController将被Storyboard reference取代。点击它，我们看看它的属性，如图:
![](http://ww2.sinaimg.cn/large/6bf526ffgw1exoz7tt4dnj20g107eq3b.jpg)
右边的属性表示这引Storyboard reference指向的Storyboard。

我们也可以拖一个Storyboard Reference出来，然后在它属性中选择指向的Storyboard建立关系。

2. 不同Storyboard中相互引用UIViewController

在Main.storyboard中拖一个UIViewController，并设置其Storyboard ID为ThreeViewController（可以为任意值）, 如下图:
![](http://ww2.sinaimg.cn/large/6bf526ffgw1exozk23pvoj209v075dgk.jpg)

然后在First.storyboard中拖一个Storyboard Reference, 并设置其属性，如图：
![](http://ww1.sinaimg.cn/large/6bf526ffgw1exozvaxkt9j20ed07kgm0.jpg)

Storyboard Reference如果没有设定Refenenced ID， 那么就指向Storyboard的Initial View Controller, 指定了，就指向指定的View Controller.

很好用吧。
