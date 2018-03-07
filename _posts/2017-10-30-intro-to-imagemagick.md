---
title: ImageMagick 简单入门
date: 2017-10-30 22:33:27
tags:
  - ImageMagick
  - 图像处理
categories: 图像处理
---

[ImageMagick](http://www.imagemagick.org/script/index.php) 是一款可以运行在 Linux/Unix/Windows/MacOS （现在甚至支持 iOS、Android）下的免费开源图片处理程序。使用 ImageMagick 可以进行图片的拉伸、裁剪、缩放、格式转换、生成 GIF 、添加文字/图案等各种操作。


## 安装

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

## 使用

### 压缩 JPEG 图片

```
# 查看系统中安装的 iamgeMagick 的版本
$ convert -version
```

```
# 图片质量 70%
$ convert -quality 70 -strip 1.jpg 1.1.jpg
```

### 裁剪图片

https://www.ibm.com/developerworks/cn/linux/l-graf2/index.html

http://www.netingcn.com/imagemagick-crop.html

```
# 从左上角（0, 2px）开始，裁剪出一张 280x101 像素大小的图片
convert pub.png -crop 280x101-0+2 pub-2.png

# 假设我们有一张4160X2340 的图片，想要裁剪掉一部分，保留居中的部分，可以像下面这样，意思是将高度变为 1900 像素
convert outside.jpeg -gravity center -crop 4160x1900+0+0 outside-3.jpeg

# 从图片的右下角开始，以向左0像素、向上91像素为起点，裁剪出一张尺寸为 3200×1371 像素大小的图片出来
convert outside.jpeg -gravity southeast -crop 3200x1371+0+91 outside-2.jpeg
```

### 生成 GIF

### 将图片转换为 PDF

场景1，在某个目录下，将所有的 `jpg` 图片，合并为一个 PDF 文件

```
$ convert *.jpg foo.pdf
```

场景2，把某张图片转换为 PDF

```
$ convert pic.png pic.pdf
```

上面两个生成 PDF 的过程都不会修改原图。[参考](https://stackoverflow.com/questions/8955425/how-can-i-convert-a-series-of-images-to-a-pdf-from-the-command-line-on-linux)

### png8 & png24

使用 imageMagick 可以方便地把 PNG32 图片转换为 PNG24 或者是 PNG8 格式的图片。

Mac 系统的截屏，默认是 PNG24。

```
$ convert douban-movie.png png8:douban-movie-8.png
$ ll
-rw-r--r--  1 wzl  staff   383K  1 18 10:17 douban-movie.png
-rw-r--r--  1 wzl  staff    77K  1 18 10:22 douban-movie-8.png
```

可以看出，大概压缩到原体积的20%。质量嘛，自然是下降了很多。


### pngcheck

可以使用 pngcheck 工具对 PNG 的信息进行查看。Mac 下可以方便地使用 homebrew 进行安装：

```
# 安装
$ brew install pngcheck

==> Downloading https://homebrew.bintray.com/bottles/pngcheck-2.3.0_1.sierra.bottle.tar.gz
/usr/local/Cellar/pngcheck/2.3.0_1: 6 files, 153.9KB

# 查看图片信息
$ pngcheck douban-movie.png

# 输出信息
OK: douban-movie.png (1440x822, 24-bit RGB, non-interlaced, 89.0%).
```

更多使用说明，在其[官网](http://www.libpng.org/pub/png/apps/pngcheck.html)
