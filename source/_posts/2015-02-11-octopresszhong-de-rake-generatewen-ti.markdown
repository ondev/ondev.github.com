---
layout: post
title: "Octopress中的rake generate问题"
date: 2015-02-11 14:14:53 +0800
comments: true
categories: Blog
keywords: Octopress
description: 
---
好久没有写博客了，今天想写突然发现rake generate出问题了，可能是系统升级造成的。在此记录一下。

随便我就重新装了一下octopress环境。
以前的环境我是用的rvm安装的ruby，这种方式安装不了最新的ruby,所以我将rvm删了,命令是

```
rvm implode
```

接着用brew来安装了最新的ruby

```
sudo brew install ruby
```

然后到Octopress中安装bundler

```
sudo gem install bundler
```

由于以前我使用的rake 版本是0.9.6,  现在我Mac上的rake版本却是10.4.2, 所以到Gemfile中修改rake版本

```
gem 'rake', '~> 10.4.2'
```
rake版本可以通过下面命令查看

```
rake -V
```

下面总结一下Octopress 2.0的rake命令

```
rake new_post["XXX"]  //新建博文
rake new_page["xxx"]  //新建页面
rake install ["theme name"]   //.theme目录下的主题
rake generate   //生成静态html文件
rake preview    //本地浏览 http://127.0.0.1:4000
rake watch    //没用过，不太清楚作用
rake deploy   //发布
```

Octopress 3.0将要发布了，到时候就不用rake命公了，[这儿](http://octopress.org/)抢先了解。

>时间仓促，难免有不少错误，还往指正。