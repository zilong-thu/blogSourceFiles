---
title: 用Cordova+HTML5开发安卓应用（01）
date: 2015-03-11 20:39:36
tags: Hybrid App
categories: Cordova
---
[Cordova](http://docs.phonegap.com/en/edge/index.html) 提供了基于HTML5、JavaScript开发跨平台的移动应用的解决方案。

本文记录从开发环境的搭建，到跑起Hello World程序的过程。

> Cordova简介：
>
> Apache Cordova is an open-source mobile development framework. It allows you to use standard web technologies such as HTML5, CSS3, and JavaScript for cross-platform development, avoiding each mobile platforms' native development language. Applications execute within wrappers targeted to each platform, and rely on standards-compliant API bindings to access each device's sensors, data, and network status.

<!-- more -->

## 开发环境搭建
在Windows 8下面，搭建基于Cordova的安卓开发环境。

##Java 环境
首先，应该已经安装了Java JDK，并且配置好了环境变量。安装与配置方法参见博文 [《Java Hello World》](/2015/02/04/java-hello-world/)。

##Android SDK
其次，下载谷歌的Android SDK。

Android SDK包的发布名称格式是这样的：`adt-bundle-<os>-<arch>-<ver>`，即“adt-bundle-操作系统-架构-版本”。在北邮人BT下载的，速度还很快多。注意，安卓SDK要与Java JDK的支持位数一样，都是32位的，或者都是64位的。这个压缩包集成了Eclipse和ADT插件，以及安卓虚拟机，只需要先安装Java环境即可使用。

Eclipse不需要安装，直接使用。

配置过程参考非常详细的一个帖子： [五步搞定Android开发环境部署——非常详细的Android开发环境搭建教程](http://www.cnblogs.com/zoupeiyang/p/4034517.html) 。

###Great Fire Wall
防火墙是安卓开发入门的一个小门槛。可以使用映像解决防火墙的问题，例如： http://sdk.gdgshanghai.com  8000 。或者根据DNS解析的原理不定期地及时更新本地的hosts文件，hosts源汇总见：__http://levi.cg.am/archives/3553__ 。不得不说，真乃业界良心啊。

###环境变量

将下面的路径加到系统的环境变量Path中：

```
;C:\AndroidSDK\sdk\platform-tools;C:\AndroidSDK\sdk\tools
```

###Android SDK关联到Eclipse
就是ADT（Android Develope Tools）的安装。

Help菜单 -> Install New Software -> Add...

```
Nmae: ADT
Location: http://dl-ssl.google.com/android/eclipse/
```

##配置Cordova
具体按照[PhoneGap Documentation中的Android Platform Guide](http://docs.phonegap.com/en/edge/guide_platforms_android_index.md.html#Android%20Platform%20Guide)来操作。

这个文档太详细了。懒得写了。

就是要注意：不出意外，ant是一定要安装的。

##安装ant
配置过程很简单，获取、解压、环境变量。见 [Installing Apache Ant](http://ant.apache.org/manual/index.html)。

ant的获取： [Downloading Apache Ant](http://ant.apache.org/bindownload.cgi)

将`%ANT_HOME%/bin`添加到环境变量Path中。