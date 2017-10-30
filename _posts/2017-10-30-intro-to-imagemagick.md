---
title: ImageMagick 简单入门
date: 2017-10-30 22:33:27
tags: ImageMagick
categories: Linux
---

[ImageMagick](http://www.imagemagick.org/script/index.php) 是一款可以运行在 Linux/Unix/Windows/MacOS （现在甚至支持 iOS、Android）下的免费开源图片处理程序。使用 ImageMagick 可以进行图片的拉伸、裁剪、缩放、格式转换、生成 GIF 、添加文字/图案等各种操作。

MacOS 下可以方便地使用 Homebrew 进行安装：

```
$ brew install imagemagick

# 查看使用帮助
$ man magick
$ man convert
```

ImageMagick 的最新版本目前已经是 7.* 以上了。建议安装最新版，因为之前它还暴露过了一个严重漏洞，在最新版本里该漏洞是已经修复了的。它的源码放在 Github 上面维护：https://github.com/ImageMagick。

<!-- more -->

