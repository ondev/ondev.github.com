---
layout: post
title: "Parse Json to Objective_C Model"
date: 2014-02-08 22:51:42 +0800
comments: true
categories: [iOS]
---
>本站QQ技术群:<疯狂IT人>93916004

在开发server/client的移动应用程序时候，往往用json来转输数据。 最近用空闲时间写了一个根据json自动生成Objectiv_c的源码程序。原理是借鉴WSDL2Objc的源码，用到了STSTemplateEngine. 源码托管在Github:[Json2Objc](https://github.com/ondev/Json2Objc)

运行程序，然后有三个需要指定的东西：需要指定json文件，生成的文件的存放路径，生成文件的名字。

然后parse，如果没有错误发生，就会在Finder中打开生成的目录。

如何用生成的文件，可以参看参看TestCase。

有任何不明白的地方请留言。

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004