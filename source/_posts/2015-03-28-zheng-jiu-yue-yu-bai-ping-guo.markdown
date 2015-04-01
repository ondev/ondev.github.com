---
layout: post
title: "拯救越狱白苹果"
date: 2015-03-28 10:39:06 +0800
comments: true
categories: iOS
keywords: [白苹果]
description: 
---
今天要一大早，太操蛋了，好久没玩越狱了，用了一个重启命令

```
killall -HUP SpringBoard
```
结果成了白苹果了。

然后我在ssh中用reboot重启，还是白苹果，最后用Home+开机键关机后重启还是不能解决。

##重刷系统

首先强制关机Home+Power键
然后按一直按住Power键，直到屏上出现白苹果时(不要松开Power键)再按住Home键，当屏幕黑屏后松开Power键(Home键不要松)，直到电脑ITunes上有反应时才松开。

如果不成功多次几次，顺序是Power->Power+Home->Home，先按先放，后按后放的原则，理一下过程就清楚了。<br>

当ITunes检测到Recovery Mode，Mac系统上按住Option键(Windows上按Shift键)并点击Restore，在弹出的对话框中选你下载的iOS固件(ipsw文件)，然后就是等待了。

##安全模式
1、如果遭遇循环重启或白苹果，同时按住电源键和 Home 键<br>
2、当苹果 Logo 出现又消失后松开两个键<br>
3、同时按住电源键和音量增加键开机<br>
4、当苹果 Logo 出现时，松开电源键，但一直按住音量增加键直至启动完成<br>
5、前往 Cydia 卸载所有你认为和循环重启有关的插件<br>
6、如果问题未能解决重复以上五步<br>
7、问题解决

我只知道这两种解决白苹果，如果你有更好的方法还望告之。

>时间仓促，难免有不少错误，还往指正。