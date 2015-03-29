---
layout: post
title: "ionic简明教程一"
date: 2015-03-29 11:17:10 +0800
comments: true
categories: ionic
keywords: [ionic,hybrid]
description: 
---

ionic是一个Hybrid框架，关于它的介绍可以看它的[官网](http://www.ionic.io)<br>

要使用这个框架，得配置环境。<br>
需要安装nodejs,自行google。<br>

安装cordova, ionic，cordova是什么？你应听说过PhoneGap吧，就是它，同一个东西。

```
sudo npm install -g cordova ionic
```

在Mac上开发测试iOS需要模拟器，还需要安装ios-sim

```
sudo npm -g install ios-sim
```
而Android则需要Ant, 在[这儿](http://ant.apache.org/bindownload.cgi)下载并解压放到本地目录,然后配置环境变量

```
export ANT_HOME=/usr/local/apache-ant-1.9.4
export PATH=${PATH}:${ANT_HOME}/bin
```
查看ant版本

```
ant -version
```


下面就可以通过ionic的CLI来创建工程:

```
ionic start myApp tabs
```

其中myApp是工程名字，tabs是内置模板中的一种，与xcode创建iOS工程的一样，有模板选择。inoic内置模板包括:blank,tabs,sidemenu

下面编译并运行项目:

```
cd myApp
$ ionic platform add ios
$ ionic build ios
$ ionic emulate ios
```
发果一切顺利，就会启动iOS的模拟器运行工程。

同时也可以使用浏览器来测试:

```
ionic serve
ionic serve --lab
```

编译与运行一步到位

```
ionic run ios 
```

更新cordova与ionic

```
npm update -g cordova ionic
```

更新项目中使用的js库

```
ionic lib update 
```

查看ionic版本

```
ionic -v 
```

ionic run android如果出现Failure [INSTALL_FAILED_OLDER_SDK],表示你用高版本的sdk编译，安装到低版本的android系统上，所以需要重新配置模拟器的target api level; 如果出现HAX is not installed on this machine (/dev/HAX is missing).表示没有安装Inter X86 Emulator Accelerator(HAXM installer),在Android SDK Manager下的Extras里。 这儿ADT里显示是installed，其实是没有安装的，需要到android sdk目录下的extras/intel/Hardware_Accelerated_Execution_Manager 中手动安装。


>时间仓促，难免有不少错误，还往指正。