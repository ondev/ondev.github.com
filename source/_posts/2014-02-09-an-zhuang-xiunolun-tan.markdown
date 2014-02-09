---
layout: post
title: "安装xiuno论坛"
date: 2014-02-09 10:33:51 +0800
comments: true
categories: [Mac, PHP]
---
最近想开一个论坛，于是找了一些常用bbs开源框架，比较成熟的有Discuz, phpWind… 无意间看到有人推荐xiuno, 特点是小巧，速度快。由于我不想建一个大型的论坛，所以选择了速度与小巧的xiuno.

首先是下载[程序](http://bbs.xiuno.com/thread-index-fid-2-tid-4159.htm). 解压放到你的站点目录。 然后在浏览器里定位到你的bbs目录，就会提示你进行安装，如果没有出现，则是因为你的文件目录没有写权限 修改权限命令： 

```
chmod -R 777 ./xiuno 
```

安装成功后，进入后台，看到有一个警告。php.ini 中未设置 upload_tmp_dir，可能会导致上传失败。

我的vps是unbuntu的系统，所以到/etc/php5/apache2目录下修入php.ini, 将upload_tmp_dir前面的;号去掉，;号表示注释。 

```
 sudo vim php.ini 
 ``` 
如果是Mac系统，php.ini的目录是/etc, 下面有一个php.ini.default文件 

``` 
cp php.ini.default php.ini sudo vim php.ini 
```

一个vps如果要想建多个站，那么需要设置virtual host. 请先配置好virtual host，然后再进行站点安装，否则会出错，大家一定注意。