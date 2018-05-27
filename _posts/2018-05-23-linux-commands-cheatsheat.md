---
title: Linux 命令行笔记（3）
date: 2018-05-23 01:24:38
tags: Linux
category: Operating System
---

读《鸟哥的Linux私房菜（第三版）》所做的部分实际操作及笔记。

<!-- more -->

## 纯文本文件的查阅

### cat

```
# 会在每一行前面打印行号
$ cat -n file_name.txt
```

cat、tac、nl 的问题在于，一次性全部将文件内容输出到屏幕上。大文件处理起来会非常慢。应该使用 more/less。

### more & less

输入 `more` 之后，会进入 more 程序。如果文件的内容超出了屏幕的显示范围，那么会有一些按键操作用于交互:

```
$ more file_name.txt
```

less 是比 more 使用得更多的一个程序。

### tail

如果只想查看文件的最后若干行，可以这样（只查看文件的最后12行）：

```
$ tail -n 12 file-name.txt
```

如果文件的内容在持续地变化，好比日志文件，想要实时地查看其尾部内容，则要加入 -f 参数

```
$ tail -n 20 -f file-name.log
```

`tail` 程序需要使用 `ctrl` + `c` 来退出。

