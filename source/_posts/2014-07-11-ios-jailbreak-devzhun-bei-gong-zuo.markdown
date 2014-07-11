---
layout: post
title: "iOS jailbreak Dev准备工作"
date: 2014-07-11 16:58:09 +0800
comments: true
categories: [Jailbreak,iOS]
keywords: [iOS Jailbreak,iOS越狱开发,programming,Dev]
description: 
---

>本站QQ技术群:<疯狂IT人>93916004

安全越来越重要了，所以我也在开始着手研究iOS安全。iOS的安全很大一部份是在iOS越狱的基础上进行的，所以对基设备进行越狱，并准备向安全领域进军。

1.iOS越狱设备，这我就不多说了,(有风险，自行承担)
2.在iOS越狱设备上安装openssh, SBSettings, BigBoss Recommended tools, MobileTerminal,这些可以通过Cydia安装

下面安装clutch,class-dump-z

```
ssh root@you ios device ip
apt-get upgrade
wget http://networkpx.googlecode.com/files/class-dump-z_0.2a.tar.gz
tar -xvzf class-dump-z_0.2a.tar.gz
cd iphone_armv6
cp class-dump-z /usr/bin
chmod 777 /usr/bin/class-dump-z
```
可以用sftp将clutch上传到iOS设备里。Clutch在[这儿](https://github.com/KJCracks/Clutch)哦

从appstore上下载的app是加了密的，加了密的app，class-dump-z就无能为力了，所以需要clutch将基解密。

以2048为例

```
clutch 2048
cd /User/Documents/Cracked
unzip /User/Documents/Cracked/2048.ipa -d 2048
class-dump-z 2048/Payload/2048.app/2048 > class-info-2048
```


3.在Mac上安装theOS,iOSOpenDev,Cycript


就这些了，以后再加用到的。


>时间仓促，难免有不少错误，还往指正。
