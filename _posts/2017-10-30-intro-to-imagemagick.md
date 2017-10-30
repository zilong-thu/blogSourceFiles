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

magick 和 convert 应该是同一个程序。

ImageMagick 的最新版本目前已经是 7.* 以上了。建议安装最新版，因为之前它还暴露过了一个严重漏洞，在最新版本里该漏洞是已经修复了的。它的源码放在 Github 上面维护：https://github.com/ImageMagick。

<!-- more -->

## 压缩 JPEG 图片

```
# 查看系统中安装的 iamgeMagick 的版本
$ convert -version
```

```
# 图片质量 70%
$ convert -quality 70 -strip 1.jpg 1.1.jpg
```

## 裁剪图片

https://www.ibm.com/developerworks/cn/linux/l-graf2/index.html

```
convert pub.png -crop 280x101-0+2 pub-2.png
```

## 生成 GIF

