---
layout: post
title: "iOS App重新签名方法介绍"
date: 2014-02-22 14:38:26 +0800
comments: true
categories: [iOS]
keywords: iOS,App签名,重签名,resign,codesign
description: 教大家如何重签名APP
---

>本站QQ技术群:<疯狂IT人>93916004

今天要给大家介绍两种app重新签名的方法, 做一下记录方便需要的朋友。<br>
1. [通过Xcode重答名](#md-1) <br>
2. [直接修改已签名的iPA文件](#md-2)

<!-- more -->


###1.<a name="md-1" id="md-1">通过Xcode重答名</a>

用Xcode打过包的朋友都知道，只需要在Xcode中点两下，就可以签名了，这种方法比较简单直观。今天要介绍的是打包后，不同Mac上重新签名的方法，即传播Xcode Archive.<br>

在Xcode中archive成功后，点击Distribute后,我们选择第三个选项：Export as Xcode Archive:<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1eds5kjya4pj20o30h4767.jpg)<br>

然后我们就会得到一个后缀为xcarchive的文件，用于Mac间传递。现在我们就可以将这个.xcarchive文件发给其它同事，同事收到后双击，就可以在Xcode中打开，接着它就可以Submit to the iOS App Store或Save for Enterprise or Ad Hoc Deployment. 这样就可以达到重签名的目的 <br>


###2.<a name="md-2" id="md-2">直接修改已签名的iPA文件</a>

其实iPA文件就是zip文件，只是后缀不同而已。要重签名，我们需要准备证书与provision profile， 证书直接在Keychian里管理，provision profile与证书是对应关系。 真机调试或提交过App到App Store的朋友都应不会陌生。<br>

下面直接说步骤：<br>
a.解压iPA文件<br>
b.删掉旧的签名文件<br>
c.拷贝新的provision profile替换旧的embedded.mobileprovision<br>
d.用codesign命令重签名<br>
f.重新zip为iPA文件<br>

下面有一个脚本，它完成了上面5步骤：<br>

```
#!/bin/sh

if ! ([ -f "$1" ]); then
echo \"${1}\"文件不存在
exit
fi
ipaName=${1%.ipa}
if [ "$ipaName" = "$1" ]; then
echo \"${1}\"不是ipa文件
exit
fi

## step 1, unzip ipa file
unzip ${ipaName}.ipa

## step 2, remove old codesign
rm -rf Payload/*.app/_CodeSignature/

## step 3, copy new provision profile
cp 123.mobileprovision Payload/*.app/embedded.mobileprovision

## step 4, codesign with new certificate and provision
(/usr/bin/codesign -f -s "iPhone Developer: XXXXX (XXX)" --resource-rules Payload/*.app/ResourceRules.plist Payload/*.app/) || {
## if code sign error, will to here
echo failed
rm -rf Payload/
exit
}

## step 5, zip it
zip -r ${ipaName}abc.ipa Payload/
rm -rf Payload/

```

需要注意脚本中有两处需要你们手动修改，第一处是123.mobileprovision，这个文件需要与脚本放同一目录，第二处是XXXXX (XXX), 这儿XXX改为你keychian里有的证书，只需要改XXX, 括号保留。<br>


###用法
保存并修改上面的脚本为resign.sh，再将需要重签名的xxx.ipa与123.mobileprovision文件置于同一目录。然后运行命令：<br>

```
chmod 777 resign.sh
./resign.sh xxx.ipa
```
成功后，在目录里会多一个重签名的abc.ipa<br>

---
>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004