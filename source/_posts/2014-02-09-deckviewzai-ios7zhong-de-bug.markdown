---
layout: post
title: "DeckView在iOS7中的bug"
date: 2014-02-09 00:52:32 +0800
comments: true
categories: 
---

前面有两篇文件介绍了适配iOS7的工作，也总结了其最主要的改变就是view全屏模式的布局方式。
我iOS开发的朋友可能用过[ViewDeck](https://github.com/Inferis/ViewDeck)这个库，它可以做出类似facebook那样的左右滑动的效果。当你升级到iOS7后，将会有一些问题。这篇文章将介绍我遇到的问题及解决方法。

首先，我说说我用viewdeck的ui布局， window的rooterViewController是一个UITabBarViewController, 然后IIViewDeckController是UITabBarViewController的子view. 然后每一个IIViewDeckController的left,center是UINavigationController. 还有一点需要说明， UITabBar的background Image是自定义的一张图片。


这样的布局在iOS6上显示正常的，在iOS7 3.5 retina的Simulator上显示也是正常的，但是在iPhone4s上显示不正确了，IIViewDeckController的left与center的view高度刚好少44。也就是说这个问题只会在设备上出现。我debug了一下，发现造成这个问题的原因是UITabBar自定义了背景图片，如果不自定义则ok.但是我们的程序是需要自定义UITabBar的背景的。由于我看到刚好少了44px,所以我就试着在IIViewDeckController的viewDidLoad中加了一句代码：

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        self.extendedLayoutIncludesOpaqueBars = YES;
    }
```

这样就可以成功运行了。 这句话的意思就是，是否延升到Tabbar区域。默认是NO的，由于Tabbar是44px,这样修改刚好可以fix这个问 但是最让我费解的就是在Simulator上没有这个问题，在Device上有问题，不过问题总算解决了。

我也把我做的测试工程放到了[GitHub](https://github.com/ondev/TestViewDeck), 大家可以下载分别运行到Simulator与Device, 然后把上面代码注释掉再运行做比较。 我的开发环境是:Version 5.0 (5A1413) + iOS7

如果你的测试结果与我不一样，请告诉我