---
layout: post
title: "Mac下配置Mantis"
date: 2014-02-11 11:24:19 +0800
comments: true
categories: [Mac]
---

安装我就不介绍了，与传统的php应用安装一样，放到对应的site目录下，然后在浏览器方问进行安装。
安装成功后会有红色的warning,

###去掉Warning
然后修改phi.ini 
```
sudo vim /etc/php.ini
```
中的值

```
date.timezone = Asia/Shanghai
```
然后重启apache

```
sudo apachectl restart
```

###中文支持

修改config_defaults_inc.php

```
$g_default_language		= 'chinese_simplified';
```

如果想方便，你可以下载一键安装包
[https://bitnami.com/stack/mantis/installer](https://bitnami.com/stack/mantis/installer)

与svn的整合，我没有配置成功，还是介绍一下我所做的：
用到了mantis的[插件](https://github.com/mantisbt-plugins/source-integration)
Mac自带的svn server，见参考。

有成功配置的朋友，希望能分享一下。

参考：
[Mac下配置svn server](http://xiayong.blog.51cto.com/6292420/1088790)
[VCS plugs](https://github.com/mantisbt-plugins/source-integration)

> Written with [StackEdit](https://stackedit.io/).