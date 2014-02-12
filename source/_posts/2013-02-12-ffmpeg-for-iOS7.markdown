---
layout: post
title: "FFmpeg for iOS7"
date: 2014-02-12 11:24:19 +0800
comments: true
categories: [iOS7, FFmpeg]
---

最近花了点时间来学习ffmpeg for iOS.
首先是编译，在[github](https://github.com/kewlbear/FFmpeg-iOS-build-script)上已有人写了编译脚本，我试了，能成功编译。

接着是使用ffmpeg, 在[github](https://github.com/kolyvan/kxmovie)上找到一个工程，不过没有更新了（需要ruby环境），它的rake在xcode5环境下是编译不过的。不过我已修改了它的编译脚本，并支持xcode5+iOS7，rake可以直接使用, 大家可以在我的[github]()上下载， 我已向原作者提交了pull request。库没有加x86_64，需要的参考arm64自己加吧，这样也明白脚本编译的原理。 <br>
这个工程比较不错用opengles+coreaudio的方式播放视频，值得大家研究研究。

ffmpeg编译通过了，接下来就是ffmpeg的世界。

大家有问题可以给我留言交流也可以加我的qq群交流(疯狂IT人):93916004


> Written with [StackEdit](https://stackedit.io/).