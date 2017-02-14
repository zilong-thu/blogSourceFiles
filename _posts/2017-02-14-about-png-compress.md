---
title: PNG 图片压缩及若干工具对比
date: 2017-02-14 20:05:00
tags: 图片压缩
categories:
---

原始图片的体积是433KB，格式为 PNG，尺寸为 2560×920。

## imagemin-pngquant

第一个找到的工具是 [imagemin](https://github.com/imagemin/imagemin)。不过单纯使用这个工具并不会获得体积更小的PNG图片，需要配合[imagemin-pngquant](https://github.com/imagemin/imagemin-pngquant)一起使用才可以，后者是一个有损压缩工具。像这样：

```
const fs = require('fs');
const imagemin = require('imagemin');
const imageminPngquant = require('imagemin-pngquant');

const PATH = './images/distributionBg.png';

imagemin([PATH], './build', {
  plugins: [imageminPngquant({quality: '90-100'})]
});
```

通过 `{quality: '99-100’}` 这样的配置，将图片压缩至 156 KB。肉眼的对比也会发现，有可以察觉的差别，但是并不影响图片质量。

相关的 [pngquant](https://pngquant.org/) 是一个命令行有损压缩（lossy compression）工具。可以通过 homebrew 进行安装：

```
$ brew install pngquant
```

## ImageOptim

上面是一种自动化的方式，还找到了一个免费的 GUI 工具：[ImageOptim](https://imageoptim.com/howto.html)。在这个程序的帮助页面，可以看到其优化原理：

> ImageOptim 是这些应用程序的前端界面（GUI）：
> 
> * Zopfli
> * OptiPNG
> * PNGCrush
> * AdvPNG
> * JpegOptim
> * JpegTran
> * MozJPEG
> * 及 PNGOUT
> ImageOptim 会运行它们，最后自动选择最小的文件。

对同一张图片的优化结果，ImageOptim 采用无损压缩，体积为 207 KB。

## Zopfli

> Zopfli is an encoder implementation of DEFLATE, a compression method commonly used in PNG format (among many other usages, e.g. ZIP, etc), designed to produce the likely smallest compressed output. Since it is a lossless transformation, a PNG file that is recompressed with Zopfli still retains all the pixels as expected.

```
$ git clone https://github.com/google/zopfli.git
$ cd zopfli
$ make zopflipng
```

编译完成之后，可以在本目录下生成可执行文件 zopflipng。

```
$ ./zopflipng images/bg.png build/bg_zopfli.png
```

433 KB 的图片，可以被压缩到 207 KB。可知，在前面用 ImageOptim 进行压缩时，其选择了 Zopfli 的压缩结果。

阅读资料：

+ [http://edupertuis.net/2016/01/12/playing-with-zopfli.html](http://edupertuis.net/2016/01/12/playing-with-zopfli.html)

## PNGOUT

没找到资源

## OptiPNG

Mac OS X可以用 `homebrew` 安装：

```
$ brew install optipng
```

在最高压缩率情况下运行：

```
$ optipng -o7 ./images/bg.png -out build/bg-o7.png
```

可以将该图片由 432 KB 压缩至 240 KB。

一些阅读资料：

+ [使用OptiPNG最佳化PNG](http://blog.roachking.net/blog/2013/01/30/optimization-with-optipng/)
+ [optipng(1) - Linux man page](https://linux.die.net/man/1/optipng)

PS：node 如果要用，可以先安装并按照它的示例代码来运行（不过，我实际上没有运行成功）：

```
$ npm install --save imagemin-optipng
```

## pngcrush

```
$ brew install pngcrush
```

可以像下面这样使用 pngcrush：

```
$ pngcrush -reduce -brute build/bg_crushed.png build/bg_crushed_2.png
```

一些阅读资料：

+ [官网](http://pngcrush.com/)
+ https://zoompf.com/blog/2014/11/png-optimization
+ http://icyleaf.com/2012/03/pngcrush-usage-with-ios-apps/


## 其他参考文章
+ [png 图片压缩工具ImageOptim是如何压缩图片的？](https://www.zhihu.com/question/23752454)
