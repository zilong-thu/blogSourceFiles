---
title: Play Music with SoX on MacOS
date: 2017-04-22 23:18:00
tags: 
  - SoX
  - Command Line
categories: 
  - Linux
  - MacOS
---

## 步骤

今日突发奇想，想在命令行下播放音乐。找到了一个现成的程序，SoX（Sound eXchange）。

Mac 下面执行下面的命令即可安装：

```
brew install sox
```

安装好之后，可以通过 `play` 命令来播放音乐（这首歌是在我博客另一篇博文[HTML5 audio 实验](http://borninsummer.com/2013/11/01/html5-audio/)里面用的音乐）：

```
$ play ~/Downloads/naruto_xia_ri_xing.mp3
```

效果如下：

<img src="/images/2017/04/sox-play.png">


## 参考阅读

+ [在终端命令行下播放音乐的命令 | Linux 中国](https://linux.cn/article-1393-1.html)