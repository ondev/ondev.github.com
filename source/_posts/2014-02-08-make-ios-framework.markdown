---
layout: post
title: "Make iOS Framework"
date: 2014-02-08 22:42:06 +0800
comments: true
categories: [Mac]
---


制作framework的好处不用再说，网上也有相应的方法介绍。今天我在此总结一下常用的方法。

###1.用模板

下载[xcode模板](https://github.com/kstenerud/iOS-Universal-Framework)后按照教程安装模板，安装后就可以用xcode创建生成framework的项目工程。这种方法简单易用，没有什么难度。

###2.用static library生成

这种方法是用脚本将.a文件与头文件，包装成.framework的文件格式。 脚本与步骤在[这儿](https://github.com/jverkoey/iOS-Framework)都有详细介绍。 但是还有一步，需要做，就是将工种设置中的Build Active Architecture Only 设为NO, 否则生成的.framework 会提示找不到armv7s

###3.用bundle模拟

这个方法也是用脚本的方法，组装bundle为framewrok. 大家直接看[教程](http://jaym2503.blogspot.sg/2012/09/how-to-create-custom-ios-framework.html)

这三种方法都试了，我比较喜欢用第二种方式，因为它的来龙去脉都比较清晰，知道.framework是如何生成的。