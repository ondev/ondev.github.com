---
layout: post
title: "使用Auto Layout的坑"
date: 2014-07-23 17:40:05 +0800
comments: true
categories: [iOS]
keywords: [iOS开发,UI,Auto Layout,iOS开发遇到的坑]
description: 
---
>本站QQ技术群:<疯狂IT人>93916004

最近在研究iOS8, 用beta版的xcode创建了一个swift项目，由于iOS8加入了size classes的概念，所以当然要用这先进东西了。

创建了一个tabbar的工程，试着在storyboard中研究它，发于UITextView这个控件在w(Compact), h(Regular)iPhone竖屏的情况下，字符显示不完整（如下图所以，好像UITextView的frame比较小）。最开始以为是constraints的问题，仔细看了一下，没有发现constraints有问题。
![](http://ww1.sinaimg.cn/large/6bf526ffgw1eimvr8u4eoj20v40zo75u.jpg)<br>

由于强迫症严重，所以总感觉这样不太对。 最后终于找到了，原来是没有设置Editable属性。只需要在storyboard属性栏中将其勾中就ok了。

![](http://ww2.sinaimg.cn/large/6bf526ffgw1eimvucfa68j20eq0go767.jpg) <br>

下在是不是看着顺眼多了？ ^V^

![](http://ww1.sinaimg.cn/large/6bf526ffgw1eimvvwb33aj20t20v0dhf.jpg) <br>

然后我试着运行工程，看结果，发现第二个tab中UITextView中的字符不见了。
![](http://ww4.sinaimg.cn/large/6bf526ffgw1eimvz0q0xwj20gw0vo0ta.jpg) <br>
这是为什么呢？

经研究发现是constraints的Placeholder属性勾中了。<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1eimw0ph1taj20dy0ceab9.jpg) <br>

Placeholder就是占位的意思，在编译的时候就会去掉这个Constraint. 由于去掉了Constraint,那么就不无决定它的frame, 结果就显示不出来了。

有了这个属性，那么就可以在storyboard或xib中先用了个constraints来占位，可以去掉警告， 方便代码加Constraints的朋友使用。


虽然用Auto Layout有一段时间了，但是这些细节还不是很清楚，还得慢慢吸收。

>时间仓促，难免有不少错误，还往指正。
