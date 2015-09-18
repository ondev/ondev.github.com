---
layout: post
title: "iOS Http URL Cache清除"
date: 2014-02-09 11:23:10 +0800
comments: true
categories: [iOS]
---
>本站QQ技术群:<疯狂IT人>93916004

三行代码

```
[[NSURLCache sharedURLCache] removeAllCachedResponses];
[[NSURLCache sharedURLCache] setDiskCapacity:0];
[[NSURLCache sharedURLCache] setMemoryCapacity:0];
```

其实只需要第一行即可。

UIWebView中的cache也可以用此方法清除， 所有通过URL Loading System缓存的数据都可以清除。

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004