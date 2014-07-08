---
layout: post
title: "Ubuntu Apache的一些配置"
date: 2014-06-17 17:35:10 +0800
comments: true
categories: [Server]
keywords: [ubuntu,apache,虚拟主机,nodejs,共享80端口,SSL]
description: 
---
>本站QQ技术群:<疯狂IT人>93916004
开发过程中需要解决的问题，然后记录解决方法。

###1.ubuntu配置虚拟主机

apache2虚拟主机的好处是可以在一台服务器上，一个端口上建立多个站点<br>


```
cd /etc/apache2/sites-available
cp default test.com
```
然后vim打开test.com
主要有三处修改

```
DocumentRoot /var/www/test.com
ServerName www.test.com
<Directory /var/www/test.com>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
</Directory>
```
然后保存修改<br>

下面命令就enable这个虚拟主机<BR>

```
sudo a2ensite test.com
```

接下来重启我们的apache2<BR>

```
sudo /etc/init.d/apache2 restart
```
这下就可以通过www.test.com（你自己的域名）访问/var/www/test.com下的站点内容了。

disable这个虚拟主机的命令是:<br>

```
sudo a2dissite test.com
```


###2.ubuntu apache启用SSL
在上面我们介绍了配置虚拟主机，接着，如果我想对这个虚拟主机加入ssl功能如何办呢，其实方法很简单。假设你已经有了证书与私钥，分别是server.crt与server.key.

那么，我们只需要修改/etc/apache2/sites-available/test.com这个文件。<br>

加入以下代码

```
# SSL
SSLEngine On
SSLCertificateFile /etc/apache2/ssl/server.crt
SSLCertificateKeyFile /etc/apache2/ssl/server.key
```

就是你服务器上对应的两个文件路径<br>

接着重启apache2生效：<br>

```
sudo /etc/init.d/apache2 restart
```

这下就需要 https://www.test.com<br>来访问你的站点了。


###3.ubuntu apache与nodejs共享80端口

首先我们要开启proxy模块<br>

```
sudo a2enmod proxy_http
sudo a2enmod proxy
```

如果想关闭则用a2dismod命令,如：

```
sudo a2dismod proxy
```

配置一个虚拟主机：<br>
假如nodejs的源码放在/home/weixin目录下<br>
那下面是一个简单的示例配置:

```
<VirtualHost *:80>
  ServerAdmin admin@gmail.com
  ServerName www.test.com
 
  ProxyRequests off
 
  <Proxy *>
    Order deny,allow
    Allow from all
  </Proxy>
 
  <Location />
    ProxyPass http://localhost:8001/
    ProxyPassReverse http://localhost:8001/
  </Location>
 
  DocumentRoot /home/weixin
  <Directory "/home/weixin">
    AllowOverride All
  </Directory>
 
  ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
  <Directory "/usr/lib/cgi-bin">
    AllowOverride None
    Options ExecCGI -MultiViews +SymLinksIfOwnerMatch
    Allow from all
  </Directory>

  ErrorLog /var/log/apache2/error.log
  # Possible values include: debug, info, notice, warn, error, crit,
  # alert, emerg.
  LogLevel warn
  CustomLog /var/log/apache2/access.log combined
  ServerSignature On
</VirtualHost>
```


接着重启apache2生效：<br>

```
sudo /etc/init.d/apache2 restart
```

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004