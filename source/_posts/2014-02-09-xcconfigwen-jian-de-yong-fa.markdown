---
layout: post
title: "xcconfig文件的用法"
date: 2014-02-09 11:24:19 +0800
comments: true
categories: [Xcode]
---

>本站QQ技术群:<疯狂IT人>93916004

xcconfig文件正如其名字一样，就是xcode里的config文件。 我们在开发过程中，需要配置一些参数，这些都可以在xcode工程的setting对项目进行配置，xcconfig就是将这些配置项以文件的形式独立出来，方便共享与配置。比如两个项目用到相同的配置，那么只需要在xcode中选择对应的xcconfig文件即可，方便与灵活共享，所以今天需要向大家简单介绍一下。

![](http://ww4.sinaimg.cn/large/6bf526ffgw1ed8jeos42pj20vg0brjts.jpg)<br>
图所示的是我创建的一个工程，我们看iOS Deployment Target这一行下面的Debug与Release， 后面是空白的，即没有设置是哪一个iOS version.<br>
当然我们可以在xcode中进行设置，在这儿我要介绍通过xcconfig的方法设置。

首先我们创建一个Debug.xcconfig文件，名字随便起，只要你自己能区分就行了。方法与创建一个新文件类似<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1ed8jj9yo2sj20nw0f0mz7.jpg)
 
接下来我们对Debug.xcconfig文件进行配置，如何进行配置呢，有个简单的方法，就是在xcode中将要配置的文件copy过来。
首先在xcode中选中要进行配置的一行，如图<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1ed8jntemnwj20tf03pwf3.jpg)
然后command+c进行复制，接着到Debug.xcconfig中command+v粘贴。
粘贴的内容类似：

```
//:configuration = Debug
IPHONEOS_DEPLOYMENT_TARGET = 6.0

//:completeSettings = none
```
在这儿我们想配置我们的项目支持iOS6的系统。

按照这个方法配置所有你想要配置的。

配置好Debug.xcconfig后，接下来我们使xcode加载它。直接上图，大家就明白了。<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1ed8jstod1aj20vj0avgn3.jpg)
注意图中选中项。

现在再到Target的Build setting中看iOS Deployment Target去看看，就多了一列了，Debug那一行设置就是6.0.<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1ed8jz5qtswj20vo0bmacj.jpg)

网上有人已奖基本的配置文件做出来了，你也就不需要一个一个去copy, parse了。<br>
可以参看github上的[xcconfigs](https://github.com/jspahrsummers/xcconfigs)

参考：
[http://b2cloud.com.au/how-to-guides/using-a-xcconfig-file](http://b2cloud.com.au/how-to-guides/using-a-xcconfig-file)

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004