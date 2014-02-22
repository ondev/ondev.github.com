---
layout: post
title: "LevelDB for iOS"
date: 2014-02-09 11:20:55 +0800
comments: true
categories: [iOS]
---
>本站QQ技术群:<疯狂IT人>93916004

最近NO SQL型数据库比较火，本人也想了解了解。于是找到了LevelDB这个数据库,它是一个KV型的数库据，还度那是杠杠的。于是试着用一下。

它是开源的，首先得编译它。网上已有很多人成功编译了它。<br>
首先就是下载源码

```
clone levelDB repo https://code.google.com/p/leveldb/
```
这个工程里的mackfile就支持编译iOS平台，只不过很久有没有更新了，现在iOS7出来后，常用指令集分别是armv7,armv7s,arm64. 对于armv6我们可以放弃了，毕竟技术是在不断发展的，过时的就不要用了。
现在我们来修改Makefile, 主要就是去掉armv6, 然后增加armv7s与arm64.

定位到.cc.o的198行与.c.o的205行
修改为：

```
.cc.o:
	mkdir -p ios-x86/$(dir $@)
	$(CXX) $(CXXFLAGS) -isysroot $(SIMULATORROOT)/SDKs/iPhoneSimulator$(IOSVERSION).sdk -arch i686 -c $< -o ios-x86/$@
	mkdir -p ios-arm/$(dir $@)
	xcrun -sdk iphoneos $(CXX) $(CXXFLAGS) -isysroot $(DEVICEROOT)/SDKs/iPhoneOS$(IOSVERSION).sdk -arch armv7 -arch armv7s -arch arm64 -c $< -o ios-arm/$@
	lipo ios-x86/$@ ios-arm/$@ -create -output $@

.c.o:
	mkdir -p ios-x86/$(dir $@)
	$(CC) $(CFLAGS) -isysroot $(SIMULATORROOT)/SDKs/iPhoneSimulator$(IOSVERSION).sdk -arch i686 -c $< -o ios-x86/$@
	mkdir -p ios-arm/$(dir $@)
	xcrun -sdk iphoneos $(CC) $(CFLAGS) -isysroot $(DEVICEROOT)/SDKs/iPhoneOS$(IOSVERSION).sdk  -arch armv7 -arch armv7s -arch arm64 -c $< -o ios-arm/$@
	lipo ios-x86/$@ ios-arm/$@ -create -output $@
```
保存，

然后用命令进行编译
```
CXXFLAGS=-stdlib=libc++ make PLATFORM=IOS
```
或
```
CXXFLAGS=-stdlib=libstdc++ make PLATFORM=IOS
```
都可以，由于用到了c++标准库，这儿分别用了新旧不同的库而已。libc++是支持c++ 11标准的，这儿分别对应xcode中引用的两个库ibc++.dylib与libstdc++.dylib两个库。

编译成功后，在源码根目录就会有一个libleveldb.a的库文件与include的头文件目录。然后就可以试用了。这儿我引用[老谭](http://www.tanhao.me/pieces/1397.html)哥写好的wrapper，并写了一个for iOS的测试工程。
创建一个iOS的新工程，将libleveldb.a与include copy到工程目录中，然后将libleveldb.a拖到工程中，接着配置header查找目录，在xcode setting中的Header Search path中加入一行${SRCROOT}，它后面选择recursive， 表示在工程源码目录中递归遍历查找头文件。
![Screen Shot 2014-02-04 at 1.52.41 pm.png](http://user-image.logdown.io/user/6397/blog/6392/post/177919/9MjT1fJxRXCdmUCZgmio_Screen%20Shot%202014-02-04%20at%201.52.41%20pm.png)

接着在AppDelegate.m中引入老潭的wrapper头文件

```
#import "THLevelDB.h"
```
接着在didFinishLaunchingWithOptions中写测试代码。

```
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *dbPath = [[paths objectAtIndex:0] stringByAppendingPathComponent:@"leveldb_demo.ldb"];
    THLevelDB *db = [THLevelDB levelDBWithPath:dbPath];
    
    //存储字符串
    [db setString:@"成都" forKey:@"city"];
    
    //存储基本类型
    [db setInt:13579 forKey:@"count"];
    [db setFloat:30.2 forKey:@"temperature"];
    
    //存储Data数据
    NSData *data = [@"天府之国" dataUsingEncoding:NSUTF8StringEncoding];
    [db setData:data forKey:@"data"];
    
    //存储任意对象(实现了NSCoding协议的对象)
    [db setObject:[NSDate date] forKey:@"object"];
    
    
    NSLog(@"城市:%@",[db stringForKey:@"city"]);
    NSLog(@"数量:%d",[db intForKey:@"count"]);
    NSLog(@"Data:%@",[db dataForKey:@"data"]);
    NSLog(@"对象:%@",[db objectForKey:@"object"]);
```

测试工程[传送门](https://github.com/ondev/LevelDBiOSDemo)。

参考：<br>
[http://blog.rpplusplus.me/blog/2014/01/20/build-leveldb/](http://blog.rpplusplus.me/blog/2014/01/20/build-leveldb/)
[http://www.tanhao.me/pieces/1397.html](http://www.tanhao.me/pieces/1397.html)
[http://herkuang.info/blog/2014/01/28/ios%E4%B8%8B%E7%BC%96%E8%AF%91leveldb%E7%9A%84%E6%80%BB%E7%BB%93/](http://herkuang.info/blog/2014/01/28/ios%E4%B8%8B%E7%BC%96%E8%AF%91leveldb%E7%9A%84%E6%80%BB%E7%BB%93/)

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004