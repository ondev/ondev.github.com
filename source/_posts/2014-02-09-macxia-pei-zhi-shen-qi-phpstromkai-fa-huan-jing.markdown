---
layout: post
title: "Mac下配置神器PhpStrom开发环境"
date: 2014-02-09 11:00:05 +0800
comments: true
categories: [Mac, PHP]
---
php这么流行，不能不研究一下。首先得找到好用的工具，见网上大神们都推荐神器phpStorm，那我也追追风，下载了一个phpStorm 7.1。平时工作都是在mac系统，所以这儿只介绍mac系统神器的配置。

安装我就不用多说了嘛。<br>
安装成功后，接下来就运行。<br>
运行后当然是创建一个了新工程，简单一点，我们创建一个空工程。 刚才都说了，是空工程，那接着我们给它加点料，新建一个php文件，暂且命名为index.

接着我们给它点点内容，那是那样吧，最简单的，用helloworld.

```
<html>
<head>
    <title>PHP 测试</title>
</head>
<body>
<?php echo '<p>Hello World</p>'; ?>
</body>
</html>
```

接着我们点击运行
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcxz9otroj20qf0ji413.jpg)

会出现一个对话框
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcy003c4xj20iw0ikwgi.jpg)

下面有一个error,是说没有找到php解释器。点击后面的Fix，会出现如下对话框：
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcy0tu5m7j20dk0i1dgj.jpg)

看到了吧，解释器设置。 mac系统是默认安装了php的，目录是在/usr/bin,那么我们点击后面的button,然后指定php解释器目录。<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcy1eg18oj20mx0ir3zn.jpg)

配置好后点击ok。

配置好后，就有解释器了。<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcy2630cbj20d80htjrz.jpg)

理论上这样配置就已完成了，可是如果你点击下图右边中的chrome图标<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcy319hyfj20ws0jcgns.jpg)

你将会看502错误，<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcy3qaz2mj20qu07r0sv.jpg)

回到PhpStorm后，你会看到有一相警告，是说php-cgi没有找到<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcy4ae661j20lc07wdga.jpg)

解决方法就是自己安装php，不用mac安装，这样就有php开发环境了。 安装很简单，直接运行一个命令, 需要几分钟，请慢慢等待。<br>

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.5
```

新安装的php目录是/usr/local/php5/bin，接下来在PhpStorm中设置新安装的php解释器： PhpStorm->Preferences->PHP 会来到解释器设置页<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcy5cwpgaj20y40mvtbk.jpg)

设置我们安装的php解释器：/usr/local/php5/bin<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcy5zj6k8j20mx0iqgmv.jpg)

这下配置完成，把鼠标放到PhpStorm右边区域，然后点击你电脑上安装了的浏览器，就会打开浏览器显示index.php的内容<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcy6kpunej206r04imwy.jpg)

如果想从网页中触发进行调试，需要安装插件。我用的是chrome, 需要安装[Xdebug helper](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc)

到此，神器PhpStorm for Mac已配置完成，可以开始你的php之旅了。