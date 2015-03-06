---
layout: post
title: "Java开发记录"
date: 2015-03-06 15:46:32 +0800
comments: true
categories: [Java]
keywords: Java
description: JAVA
---

最近在写一个小东东，想一次开发同时运行到多个平台上，由于有UI，所以我选择了JSE + SWT. <br>


开发功能还好，没有遇到什么问题，Eclipse用一下就熟悉了.<br>

### 打包
开发完后要打包，就遇到问题了，我想打成jar包，结果Eclipse自带的export不强，所以只能google打答案。<br>
得到答案是需要一份清单列表。而还需要手功创建，看到后就醉了，不就打个包吗，弄得这么麻烦。没法只有硬上了，手动创建一个名为MANIFEST.MF的文件.<br>
下面是我的清单列表， Class-Path是工程依赖的jar包，然后这些jar包都放到该工程目录下的libs目录下。<br>
Main-Class是你的程序入口文件，即包括Main的文件。 注意Class-Path中每一行前后都有一个空格，最后一行不需要。

```
Manifest-Version: 1.0
Class-Path: libs/HttpClient/commons-codec-1.6.jar 
 libs/HttpClient/commons-logging-1.1.3.jar 
 libs/HttpClient/fluent-hc-4.3.6.jar 
 libs/HttpClient/httpclient-4.3.6.jar 
 libs/HttpClient/httpclient-cache-4.3.6.jar 
 libs/HttpClient/httpcore-4.3.3.jar 
 libs/HttpClient/httpmime-4.3.6.jar 
 libs/gson-2.3.1.jar 
 libs/jsoup-1.8.1-sources.jar 
 libs/jsoup-1.8.1.jar 
 libs/quartz-2.2.1/lib/quartz-2.2.1.jar 
 libs/quartz-2.2.1/lib/quartz-jobs-2.2.1.jar 
 libs/quartz-2.2.1/lib/slf4j-log4j12-1.6.6.jar 
 libs/quartz-2.2.1/lib/slf4j-api-1.6.6.jar 
 libs/quartz-2.2.1/lib/log4j-1.2.16.jar 
 libs/quartz-2.2.1/lib/c3p0-0.9.1.1.jar 
 libs/sqlite-jdbc-3.8.7.jar 
 libs/httpcomponents-asyncclient-4.0.2/lib/httpasyncclient-4.0.2.jar 
 libs/httpcomponents-asyncclient-4.0.2/lib/httpcore-nio-4.3.2.jar 
 libs/os/com.ibm.icu_52.1.0.v201404241930.jar 
 libs/os/org.eclipse.core.commands_3.6.100.v20140528-1422.jar 
 libs/os/org.eclipse.core.runtime_3.10.0.v20140318-2214.jar 
 libs/os/org.eclipse.equinox.common_3.6.200.v20130402-1505.jar 
 libs/os/org.eclipse.equinox.registry_3.5.400.v20140428-1507.jar 
 libs/os/org.eclipse.jface_3.10.1.v20140813-1009.jar 
 libs/os/org.eclipse.jface.text_3.9.1.v20140827-1810.jar 
 libs/os/org.eclipse.osgi_3.10.1.v20140909-1633.jar 
 libs/os/org.eclipse.swt.cocoa.macosx.x86_64_3.103.1.v20140903-1947.jar 
 libs/os/org.eclipse.text_3.5.300.v20130515-1451.jar 
 libs/os/org.eclipse.ui.forms_3.6.100.v20140422-1825.jar 
 libs/os/org.eclipse.ui.workbench_3.106.1.v20140827-1737.jar
Main-Class: iBot.Main
```

有了这个列表，这下我们来开始打包，直接用命令行吧，一下就打好，包教包会:

```
jar -cvfm yourjarname.jar MANIFEST.MF -C bin .
```
就这么一行命令打包，是不是很easy呀。首先你要cd到你的工程目录，因为我们要用到下面的bin目录，命令中-C后面有bin看到没？

经过上面一行命令的工作，如果一切顺利，在你工程目录下就多了一个yourjarname.jar文件。

这下就可以运行了，我是在Mac系统下运行，双击jar文件却不能运行，然后试着用用命令运行，

```
java -jar yourjarname.jar
```
结果报错了，说要运行在主线程，没事，我们求万能的google, 最后解决方法是加一个参数:XstartOnFirstThread

```
java -XstartOnFirstThread -jar ibot.jar
```
jar 包成功运行。


### 加载资源
程序是由代码加资源文件组成的，比如要读取icon,声间文件。由于我是新手，所以加载资源也把我难住了，幸好万能的Google我还能打开，窃喜！！！

将所有的资源放到src/res目录下，这样方便打包。因为放到src目录下，打包的时候会自动将res目录copy到bin目录下去。bin是编译后的生成文件目录，打包的时候-C后面的bin就是指这个目录。这下你明白没？<br>

接着我们在程序中加载资源吧: <br>
```
String filePath = getClass().getResource("/res/" + "icon.png").getFile();
File icon = new File(filePath);
```
这样就可以了，不过如果打包成jar后，这种方式不能成功获取资源，我们得使用另一个方法，用Stream的方式来获取。

```
InputStream is = this.getClass().getResourceAsStream("/res/" + "icon.png"); 
```

SWT中加载图片，还可以用JFace中的类:

```
ImageDescriptor.createFromFile(Main.class, "/res/login.png")；
```
返回的ImageDescriptor正是Action中要用到的。


>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004

