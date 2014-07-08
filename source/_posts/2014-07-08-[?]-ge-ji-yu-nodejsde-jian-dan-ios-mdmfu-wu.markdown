---
layout: post
title: "一个基于Nodejs的简单iOS MDM服务"
date: 2014-07-08 18:57:37 +0800
comments: true
categories: [iOS,MDM,Nodejs]
keywords: [iOS,MDM,Nodejs,iOS设备管理,iOS MDM]
description: 
---

>本站QQ技术群:<疯狂IT人>93916004

如果有效的管理自己的iOS移动设备，苹果为我们提供了MDM技术。最近小弟在研究它，有一点小经验，写了一个简单的iOS MDM服务, 服务器采用Nodejs＋Mongodb. 

大家可以试用一下,请直接[猛击这儿](https://www.sohoin.com)(记得用设备的safari打开)进入。


首先需要大家注册一个帐号，密码与帐号是管理你设备所需的。

登陆进系统后，会有两步需要你完成，一是点击安装mdm描述文件，安装成功后请进行第二步，安装udid获取描述文件，第二步会获取你设备UDID, 并加入到你的管理列表中，这样你以后就可以对其进行管理。

完成上面两步后就可以看到下面多了两列命令，一个是锁屏，一个是清除密码。目前只支持这两个命令，后续会慢慢加入。

界在很丑呀，有空美化一下，换衣大家提意见。


>时间仓促，难免有不少错误，还往指正。

