---
layout: post
title: "iOS8 APP国际化"
date: 2014-06-07 21:31:53 +0800
comments: true
categories: 
keywords: 
description: [iOS8,Localization,国际化]
---
>本站QQ技术群:<疯狂IT人>93916004

1.创建一个Localizable.strings文件 <br>
2.code中用字符串的地方使用NSLocalizedString，这与系统语言相关 <br>
3.时间显示，数字，金融与地区相关，所以需要各类NSFormater, 如NSDateFormatter, NSNumberFormatter ... <br>
3.用命令将所有NSLocalizedString返回的字符串格式化到Localizable.strings里。命令行进入工程目录（我的工程名是LocalizationTest），运行下面命令：<br>

```
find ./ -name "*.m" -print0 | xargs -0 genstrings -o LocalizationTest/en.lproj
```

4.这下就将Localizable.strings拿给翻译的人 <br>

上面是iOS7以下国际化的方法。

在iOS8中加入了一种国际通用的xliff文件，全称是 Localisation Interchange File Format（本地化交换文件格式，是xml文件）， 里面就是需要翻译的东西。打开xliff文件，里面<source></source>标签中是表示要翻译的东西，对应Localizable.strings中=号左边的字符串，<target></target>中是每种语言的翻译，对应Localizable.strings中=号右边的字符串。没有一个source标签对应一个target标签。

选中Editor -> Export For Localization就可以导出需要翻译的字符串，它会遍历整个工程，将xib，stroryboard, 代码中需要本地化字符串导出到.xliff文件中。小伙伴们再也不需要手动输入上面的命令将NSLocalizedString对应的需要翻译的字符串输出到Localizable.strings里了。 Import Localizations就是导入翻译的字符串，并自动刷新xib，.strings， storyboard中本地化文件 <br>

同时Xcode6加入了 语言Debug方式，以前测试国际化，都是修改系统的语言，然后运行程序，现在在xcode中的target的scheme中就可以修改该target运行时的语言。  这样就不需要到系统设置里去设置，更简单高效。<br>

同时xcode storyboard的预览功能更加强大了，可以实时修改预览的语言。<br>


开发越来越方便了。<br>

继续写代码，改变世界。<br>

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004