---
layout: post
title: "Mac下安装GDB"
date: 2015-02-10 16:04:35 +0800
comments: true
categories: 调试
keywords: gdb
description: 
---

1. 安装[brew](http://brew.sh/)
2. 安装gdb

```
brew tap homebrew/dupes
brew install gdb
```
将被安装到/usr/local/bin/gdb
3. 由于Mac不允许随意进入进程空间，要经过许可才可以。如果要用gdb依附进程，那就要对gdb授权才可以。所以需要创建自签证书。下面命令进行授权

```
codesign -s gdb-cert $(which gdb)
```


这下就可以使用GDB了

参考:[http://ntraft.com/installing-gdb-on-os-x-mavericks/](http://ntraft.com/installing-gdb-on-os-x-mavericks/)

>时间仓促，难免有不少错误，还往指正。