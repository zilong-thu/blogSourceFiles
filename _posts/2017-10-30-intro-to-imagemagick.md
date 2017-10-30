---
title: ImageMagick 简单入门
date: 2017-10-30 22:33:27
tags: ImageMagick
categories: Linux
---

[ImageMagick](http://www.imagemagick.org/script/index.php) 是一款可以运行在 Linux/Unix/Windows/MacOS （现在甚至支持 iOS）下的免费开源图片处理程序。MacOS 下可以方便地使用 Homebrew 进行安装：

```
$ brew install imagemagick

# 查看使用帮助——是的，imagemagick 安装好了以后，注册在 bin 里的名字是 convert
$ man convert
```

ImageMagick 的最新版本目前已经是 7.* 以上了。建议安装最新版，因为之前它还暴露过了一个严重漏洞，在最新版本里该漏洞是已经修复了的。

<!-- more -->

