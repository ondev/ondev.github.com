---
layout: post
title: "OpenGL ES简明教程之1"
date: 2015-03-13 12:48:55 +0800
comments: true
categories: OpenGLES
keywords: OpenGLES
description: 
---

最近在弄一点3D相关的东西，大概有四年没有做3D相关的东西了，也忘得差不多了。我就把最近学到的东西总结一下，记录一下，方便查询。

在iOS上开发3D的东西，有三个选择:OpenGLES,GLKit,Metal. <br>

我选择了直接用OpenGLES，因为这样更了解底层。 GLKit也是封装了OpenGLES, Metal则是新出来的苹果3D技术，也学微软了出自己DirectX标准. <br>

最新的iOS8+iPhone6是支持OpenGLES 1.0, 2.0的，3.0好像还不支持。<br>

本系列教程先介绍并学习OpenGLES 1.0，OpenGLES 1.0学完以后，再过渡到2.0, 因为学东西要先学基础，再深入，这样更容易。<br>

在学习的过程中会用到PowerVR SDK, 大家可以去[这儿](http://community.imgtec.com/developers/powervr/installers/)下载并安装。<br>

还会用到3D建模工具Blender,也请自行下载。<br>

还会用到Blender导出格式为pod的插件与导出格式为collada格式的插件。<br>

安装插件的方式，就是将下载好的插件文件复制到以下目录

```
/Applications/Blender/blender.app/Contents/Resources/2.73/scripts/addons/
```

collada格式的插件在[这儿](http://sourceforge.net/projects/colladablender/)下载.<br>

PowerVR Gen Pod的插件是在以下目录

```
/Applications/Imagination/PowerVR_Graphics/PowerVR_Tools/PVRGeoPOD/Plugins/Blender/OSX_x86/
```

>注意是复制目录下的文件

要导出pod，还需要进Blender配置一下。打开blender, 左下角的列表中选择User Preference，在打开的界面中选择Addon，在Import－Export栏中找到Import－Export：PVRGeoPOD，并将其右侧的checkbox选中，保存用户设置。

还不明白的可以看[这儿](http://blog.csdn.net/cj_gameboy/article/details/41171171)，有图有真相。

先准备好这些，下一节开始OpenGLES实战。

搞3D,给大家推荐两本书:<br>

《iPhone 3D Programming》<br>
《Learning OpenGL ES for iOS》