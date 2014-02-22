---
layout: post
title: "PhpStorm调试xiuno BBS"
date: 2014-02-09 11:13:37 +0800
comments: true
categories: [PHP]
---
>本站QQ技术群:<疯狂IT人>93916004

上一篇文章里介绍了PhpStorm的开发环境配置，并能对单页面进行调试。今天我们介绍如何调试一个web application, 就拿一个开源的php源码来做试验：xiuno bbs.

首先当然是到下载入口下载xiuno bbs的源码。然后安装吧， Mac系统自带了Apache， 所以放到站点目录下，然后浏览器访问，就可以了。在安装的时候，数据库请选择pdo_mysql, 因为如果选择mysql用的比较老的mysql操作库，在调试的时候会提示出错。

这儿我以Mac系统为例，我假设你已安装apache默认站点根目录 /Library/WebServer/Documents/

安装好了，那我们就开始用PhpStorm创建工程，选择Create New Project from Existing Files <br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcyawfkcmj20m00gfdh1.jpg)

选择xiuno源文件的目录。然后选择Project Root, 然后下一步变化可点击。<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcybhsqt7j20ob0hrjtm.jpg)

接下来设置local http server. <br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edcybzki5cj20od0hy3zw.jpg)

接下来设置http server的path<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcyck053nj20oi0htta2.jpg)

接下来设置运行环境<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcyd25tprj20tf0dl0u8.jpg)

点击加号，然后选择Php Web Application, 因为xiuo bbs是一个web application. <br> 
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcydh8g8rj20te0io0u6.jpg) <br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcye01oj7j20t80do3zi.jpg)

名字任意，但是目前server后面是红色的，点击它后面的button进行设置 <br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcyelh5jaj20l20ett9l.jpg)

点击Validate remote environment,然后选择刚才配置的Server, 然后点击下面的Validate, 如图 <br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcyf2vhrsj20f109gt9o.jpg)

接着是http目录设置，如图 <br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcyfkbk74j20tg0igdha.jpg)

这样你就可以通过PhoStorm运行或调试源码了。 <br>
xiuno的调试宏记得打开哟。 <br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcygccolzj21250d541f.jpg)

我也开始学习PHP写程序了. ^v^

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004