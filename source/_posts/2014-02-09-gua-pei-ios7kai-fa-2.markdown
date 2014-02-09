---
layout: post
title: "适配iOS7开发2"
date: 2014-02-09 00:34:13 +0800
comments: true
categories: [iOS7]
---

在前一篇文章中介绍了非autolayout模式下iOS7的适配工作。 今天来介绍一下autolayout下的适配工作。<br>

首先，我们用xcode4.6.3创建一个simgle view application. 选中Use storyboard.<br>
拖一个UIButton到view中。 由于开启了autoLayout， 在xcode4.6.3中，自动会有两个auto layout约束，分别是水平与垂直的约束：
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcfz58ucgj20o00a40u0.jpg)

运行程序效果：
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcfzva8w4j20hs0vkjrm.jpg)

可以看到是垂直方向上距status bar底部是45px，距右边是186px.<br>
现在我们用xcode5打开这个工程。会提示Upgrade storyboard，我们点击Upgrade. Upgrade后，你会发现，垂直方向上有变化了，约束是从顶部开始，不是从status bar底部开始计算。这也我们想要的结果不一样，我们还是希望约束是从status bar底部开始计算。<br>
仔细看看storyboard中的View Controller， 你会发现，这儿多了两个东东，分别是Top Layout Guide，Bottom Layout Guide. <br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcg16hbatj20ny0adaal.jpg)

选在我们的button,按住control键，然后拖到Top Layout Guide上，在弹出的对话框选择Vertical spacing
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcg1x4fjij204n03bmx3.jpg)

这样又建立了一个垂直约束，你会发现这个约束是从Status bar底部开始计算的。这个约束有点像是我们需要的，
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcg2i341lj20mt0a7wf4.jpg)

但是这个约束距status bar是25px,我们想要的是45px. 那只需要修改一下，将25px改为45px
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcg36ji0xj20vk09fmy3.jpg) 

你会发现有storyboard中有红色提示，这说明约束条件发生了冲突，因为垂直约束的值都为45，显示冲突了。由于我们只需要status bar底部开始计算的那个约束，所以我们将旧的那个删掉即可。
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcg3vzbylj20nj0akt97.jpg)

现在分别选择iOS6/iOS7来运行。都是距status bar下面45px显示button. 适配工程完成。<br>
顺便介绍一下xcode5中，用xib或storyboard布局的时候，preview iOS7与iOS7以前版本的效果。 快捷键：option+command+enter 打开Assistant editor,然后选择右边的automatic->preview->xxxxx(preview)
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcg4xvwgnj20fi0fcabq.jpg)

然后在出现的画面下面有一个切换iOS7与以前版本的切换开关
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcg5lktzgj20ca0l6t9c.jpg)

这样可以方例你实时的查看布局效果以适配不同版本的iOS.<br>
最后： auto layout是好东西，虽然只有>iOS6的版本才支持，但是我还是建意大家用auto layout,因为以后苹果也可能出现多种屏幕，到时候代码修改量将会减少。

参考：[https://developer.apple.com/library/ios/qa/qa1797/_index.html](https://developer.apple.com/library/ios/qa/qa1797/_index.html)