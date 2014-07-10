---
layout: post
title: "自定义iOS上双击Home键App切屏"
date: 2014-07-10 16:34:06 +0800
comments: true
categories: [iOS]
keywords: [screenshot, double click home, App switcher screenshot, iOS App screenshot, iOS 切屏, iOS 双击Home]
description: 
---

>本站QQ技术群:<疯狂IT人>93916004

在iOS7上，如果双击Home，会来到iOS App的switcher页面，在这儿列出了当前系统挂起的App, 上面有每个App的切屏，相信大家都熟悉这个东东了。它其实是每个App在挂起前，对App后个载屏。

那么我们如何自定义它在App Switcher上的展示呢，明白了它其实是对App的载屏，那么在它快进入后台的时候，我们改变其外观，那么载下来的图片，不就改变了吗？

明白这个原理后，那我们就试一试。

新建一个工程，准备三张图片，Defautl.png, Defautl@2x.png, Default-568h@2x.png,  将其放进工程。

我们在AppDelegate.h中声明一个UIImageView变量

```
@interface AppDelegate : UIResponder <UIApplicationDelegate> {
    UIImageView *screenShotView;
}

@property (strong, nonatomic) UIWindow *window;

@end
```

接着在AppDelegate中加入以下代码:

```
- (void)applicationWillResignActive:(UIApplication *)application
{
    if (!screenShotView) {
        screenShotView = [[UIImageView alloc]initWithFrame:[self.window frame]];
        [screenShotView setImage:[UIImage imageNamed:@"Default-568h"]];
        [self.window addSubview:screenShotView];
    }
    screenShotView.alpha = 1;
}

- (void)applicationDidBecomeActive:(UIApplication *)application
{
    screenShotView.alpha = 0;
}
```

先在模拟器上删除以app,然后再点xcode run.  双击Home, 这下你是不是在App Switcher中看到了你的Default的图片，将上面的代码中Default-568h改为其它图片, 这样达到了自定义目的, 是不是太简单了呀？


>时间仓促，难免有不少错误，还往指正。
