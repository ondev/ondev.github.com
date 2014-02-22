---
layout: post
title: "iOS获取ISO Country Code"
date: 2014-02-08 22:54:47 +0800
comments: true
categories: [iOS]
---
>本站QQ技术群:<疯狂IT人>93916004

通过SIM卡获取country code,下面是代码片段

```
NSString *(^CountryNameByISO)(NSString *) = ^(NSString *iso) {
    NSLocale *locale = [NSLocale currentLocale];
    return [locale displayNameForKey:NSLocaleCountryCode value:iso];
};

NSString *(^ISOCountryCodeByCarrier)() = ^() {
    CTTelephonyNetworkInfo *networkInfo = [[CTTelephonyNetworkInfo alloc] init];
    CTCarrier *carrier = [networkInfo subscriberCellularProvider];
    return [carrier isoCountryCode];
};


#define SIMISO                  ISOCountryCodeByCarrier()
#define CountryNameFromISO(iso) CountryNameByISO(iso)
```

这样就可以通过两个宏进行调用了

```
NSString *iso  ＝ ISOCountryCodeByCarrier();
NSString *name ＝ CountryNameFromISO(iso);
```

还有一种，是获取Device本地设置的地区。

```
#define countryISO  [[NSLocale currentLocale] objectForKey: NSLocaleCountryCode]
```

下面是获取本地设置的语言

```
#define languageISO [[NSLocale currentLocale] objectForKey: NSLocaleLanguageCode]
```

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004