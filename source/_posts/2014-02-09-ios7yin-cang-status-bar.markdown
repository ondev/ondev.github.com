---
layout: post
title: "iOS7隐藏status Bar"
date: 2014-02-09 00:49:11 +0800
comments: true
categories: [iOS7]
---
>本站QQ技术群:<疯狂IT人>93916004

iOS7中，状态栏变化比较大，hide/show状态的方法也不一样了。 在iOS7以前的版本，hide是通过以下代码实现

```
[[UIApplication sharedApplication] setStatusBarHidden:YES withAnimation:UIStatusBarAnimationSlide];

```

在iOS7中默认情况下，这个方法不成功了。到setStatusBarHidden:withAnimation:声明的头文件去看看，多了一句注释： // Setting statusBarHidden does nothing if your application is using the default UIViewController-based status bar system. 现在在iOS7中，status bar的外观默认依赖UIViewController, 也就是说status bar随UIViewController的不同而不同。在这种默认的方式下，用全局的方法setStatusBarHidden:withAnimation:是行不通的。

解决方法是在infor.plist中加入key:UIViewControllerBasedStatusBarAppearance 并设置其值为NO，这样就告诉系统，status bar不依赖于UIViewController. 这样就可以通过上面的方法来hiden status bar.

如果是在默认情况下，如果我们要hiden如何办呢。

只需要实现两个新方法

```
- (UIStatusBarStyle)preferredStatusBarStyle
{
    return UIStatusBarStyleLightContent;
    //UIStatusBarStyleDefault = 0 黑色文字，浅色背景时使用
  //UIStatusBarStyleLightContent = 1 白色文字，深色背景时使用
}

- (BOOL)prefersStatusBarHidden
{
    return NO; //返回NO表示要显示，返回YES将hiden
}
```

上面一个回调方法返回status bar显示时候的样式，下面一个回调控制是否显示status bar.

调用下面的一行代码将会触发上面的回调

```
[self setNeedsStatusBarAppearanceUpdate];
```

如果想在hiden/show之间有点动画效果，用下面的代码即可：

```
[UIView animateWithDuration:0.5 animations:^{
        [self setNeedsStatusBarAppearanceUpdate];
    }];
```
现在介绍完毕在iOS7下操作status bar.如有问题，欢迎大家指正。

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004