---
layout: post
title: "iOSOpenDev配置"
date: 2014-09-22 16:43:25 +0800
comments: true
categories: [Jailbreak,iOS]
keywords: [iOS Jailbreak,iOS越狱开发,programming,越狱开发教程]
description: 
---

>本站QQ技术群:<疯狂IT人>93916004

###配置
首先我们安装iOSOpenDev, 直接下载最新的进行安装，如果有错，可以参看[这儿](https://github.com/kokoabim/iOSOpenDev/wiki/Troubleshoot)。

越狱开发需要TheOS环境，而iOSOpenDev也是建立在TheOS的基础上的，只不过用iOSOpenDev我们就可以使用我们熟悉的Xcode进行开发了。所以我们还需要安装TheOS.打开命令行，直接运行下面命令进行安装

```
export THEOS=/opt/theos
git clone git://github.com/DHowett/theos.git $THEOS
```

利用iOSOpenDev, 我们只需要简单command+shift+i就可以将程序布置到设备上了。这个过程需要ssh连接，所以我们还需要配置ssh钥匙对。只需要一条命令即可：

```
iosod sshkey -h 192.168.1.1
```
192.168.1.1是你设备的地址，记住，你设备上一定要开启ssh哦。

iOSOpenDev的官方文档都在[这儿](https://github.com/kokoabim/iOSOpenDev/wiki/_pages)，你可以再研究研究。

接下来，我们需要dump出来的iOS header, 比如我们要hook SpringBoard. 大家可以自行dump出header，也可以到网上下载已经dump好了的。我就是直接[这儿](https://github.com/MP0w/iOS-Headers)下载的。将下载好的头文件放到/opt/theos/include目录下。

###Hello Word
现在一切都准备好了，那我们来进行我们的Hello Word.
创建一个logos Tweak工程。接着在Build Settings中iOSOpenDevDevice设置为自己设备的IP地址，设备与你的Mac要在一个局域网内。

由于我们需要引用头文件，所以还需要在Build Settings中将Header Search Paths中加一行/opt/theos/include

下面我们实现hook SpringBoard，当SPringBoard启动的时候，我们就弹出一个Alert.

在.xm中加以下代码.

```
#import <SpringBoard/SpringBoard.h>

%hook SpringBoard

- (void)applicationDidFinishLaunching:(id)application {
    %orig;
    
    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Welcome"
                                                    message:@"Welcome to your iPhone!"
                                                   delegate:nil
                                          cancelButtonTitle:@"Thanks" 
                                          otherButtonTitles:nil];
    [alert show];
    [alert release];
}

%end
```

由于用到了UIAlertView属于UIKit的东西，所以我们要链接UIKit.framework, 还有hook的库文件libsubstrate.dylib(在opt/iOSOpenDev/lib目录下面)。

中以command+B进行编译，一定会报错，下面列出我遇到的错。

1.找不到NSObject.h <br>
将#import "NSObject.h" 改为#import <NSFoundation/NSObject.h>
2.一些不能识别的数据类型，我将期改为id类型。

进行一些修改，直到编译不报错为止。

Command+Shift+i  将程序布署到设备上，SpringBoard会重启，重启后是不是看到Alert？ 没看到？ 那你一定哪儿错了，重新再来一遍吧，如果还不行，那我也帮不了你，只有去求神了。

>时间仓促，难免有不少错误，还往指正。