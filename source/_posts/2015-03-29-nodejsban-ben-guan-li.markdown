---
layout: post
title: "NodeJS版本管理"
date: 2015-03-29 20:28:04 +0800
comments: true
categories: [nodejs]
keywords: [nodejs,nodejs更新]
description: 
---

在Mac上可以通过brew来更新nodejs

```
sudo brew upgrade node
```

还有一种方法是用n, n也是nodejs的项目，首先安装它:

```
sudo npm install -g n
```

下面命令升级nodejs

```
sudo n 0.10.33
sudo n stable
sudo n latest
```



更详细的命令请查看使用帮助:

```
n --help
```


>时间仓促，难免有不少错误，还往指正。