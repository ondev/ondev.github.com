---
layout: post
title: "结队开发之多storyboard"
date: 2014-02-23 10:38:15 +0800
comments: true
categories: [iOS]
keywords: iOS,Storyboard,结队开发,团队合作
description: 解决结队开发下storyboard的问题
---

>本站QQ技术群:<疯狂IT人>93916004

Storyboard的出现，让开发变得像讲故事一样，UI间的关系流程也一目了然。它其实是xib的升级版本，将多个xib统一管理了。任何事都有双面性，Storyboard也有它的缺点。笔者就说说自己的经验，版本管理中，多人修改很容易严生冲突。storyboard中UIViewController太多，找到想要的比较困难（特别是在MBA上）。 这些缺点在结队开发中就会遇见。 如果我们用xib文件，这样将UI最小化分隔开，将后用code将这些小单元连接起来，就可以解决这个问题。如果只用xib就展现不出storyboard的故事情节能力。

<!-- more -->

本人提供两种解决方案：<br>
1.[Xib link Xib](#md-1)<br>
2.[Storyboard link Storyboard](#md-2)

其实它们之间可以两两组合，这样就可以延伸出另外两种方法:
3.Xib link Storyboard <br>
4.Storyboard link Xib <br>

###<a id="md-1">Xib link Xib</a>
这种方法其实我以[上一篇](http://www.ifun.cc/blog/2014/02/22/ioskai-fa-zhi-xibji-qiao-jie-shao/)文章已经介绍过了，大家自行阅读，在些也不多述。


###<a id="md-2">Storyboard link Storyboard</a>
这种方法与上面方法原理是一样的，利用加载storyboard时，实例化对应自定义UIViewController类，然后手动addSubview到对link view上，以达到链接的目录。 用言语表达是有点抽象，还是用代码表达吧，容易理解。<br>

在这儿用到了Github上一个人写的link代码:[RBStoryboardLink](https://github.com/rob-brown/RBStoryboardLink)。它的用法也在README中说得很清楚了。

大家将源码下载下来，里面有一个sample:LinkedTabs. 由于原作者用到了cocoapods,所以需要到源码LinkedTabs目录下， 运行一个命令行:

```
pod update
```
我还是简单说一下sample的结构。<br>
里面有三个storyboard,分别是MainStoryboard，FirstTabStoryboard， SecondTabStoryboard， 现在需要将MainStoryboard中的两个UIViewController分别与FirstTabStoryboard,SecondTabStoryboard建立连接，以达到程序运行后，这三个storyboard像是在一起的目的。<br>

打开MainStoryboard，定位到Tab1的UIViewController<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edt58mgnxij20vp0g6mzp.jpg)<br>

注意图中两个箭头，上面一个一定要填这个自定义类RBStoryboardLink， 就是个类完成了连接的任务。<br>
下面一个箭头处，是传递的属性与对应的值，在实例化RBStoryboardLink的时候传送递给实例化对象。storyboardName这个属性表示要link到哪儿去，这儿我们要link到FirstTabStoryboard，所以值为FirstTabStoryboard。还有一个可选属性sceneIdentifier, 表示要link到Storyboard中的哪一个UIViewController. 如果不传这个属性，那就link到FirstTabStoryboard中的initial View Controller.<br>

如果我们想link到FirstTabStoryboard中的第二个UIViewController那如何办呢，很简单:<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edt5kfj27gj20vp0haju2.jpg)<br>
如上图，我们先找到这个被linke的View Controller，然后在Storyboard ID那输入该View Controller的标识，如在此输入:NavController.

接着在需要link的地方加入sceneIdentifier:NavController, 见下图箭头处。<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edt5nd36iwj20vl0fktb6.jpg)<br>

这样就link成功了。

方法3与方法4只是组合出来的方法，也不在此多述了。

---
>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004