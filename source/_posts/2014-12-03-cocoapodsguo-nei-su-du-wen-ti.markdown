---
layout: post
title: "Cocoapods国内速度问题"
date: 2014-12-03 12:48:56 +0800
comments: true
categories: cocoapods
keywords: iOS,library
description: 
---

1.使用淘宝的gem镜像

```
gem sources --remove https://rubygems.org/
gem sources -a http://ruby.taobao.org/
```

2.使用国内的Cocoapods镜像

```
pod repo remove master
pod repo add master https://gitcafe.com/lloydsheng/Specs.git
pod repo update
```

3.不用每次install/update更新Cocoapods Specs

```
pod install --verbose --no-repo-update
pod update --verbose --no-repo-update
```


如果想自己搞一个镜像，[这儿](http://lloydsheng.com/post/setup-cocoapods-mirror)有方法可以参考

>时间仓促，难免有不少错误，还往指正。