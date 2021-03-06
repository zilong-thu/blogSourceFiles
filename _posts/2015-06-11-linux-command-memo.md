---
title: Ubuntu Linux 常用命令笔记
sidebar: false
date: 2015-06-11 14:15:02
tags: Linux
categories:
  - Operating System
  - Linux
---

## 写在前头

__Linux里，所有程序或系统装置都是文件__

<!-- more -->

## 入门级命令

`ls -a`  查看所有文件，包括隐藏的文件

`ls -al`  列出当前目录下所有的文件的详细权限与属性，需要以root身份登录

`ll`  列出当前目录下的所有文件和目录（包括隐藏文件和目录）的详细权限与属性

`mkdir directoryName`  创建一个目录

`rm -rf`  强行(-f)向下递归(-r)删除一个目录，不管该目录是否为空

`vi filename.txt`  创建新文

`df -h`  查看磁盘空间使用情况

`who` 查看系统有哪些用户登录了

`netstat -a`  查看网络的联机状态

`ps -aux`  查看后台执行的程序

## 目录与文件操作

### 移动文件、目录

`mv ~/dir1/dir-03 ~` 把 `dir-03` 目录放到 `~` 目录下

### 查看当前完整的工作目录路径

`pwd` 显示当前所在的目录的完整路径（print working directory）

### 查看文件、目录体积

`du` -- display disk usage statistics.

常用参数：

`-h`：Human-readable，即以可读性好的方式给出体积数值

`-s`：This option means it will not list the size for each subdirectory, only the total size.

### 查看某个程序的实际路径

`which`：locate a program file in the user's path。例如：

```
$ which git
# 输出  /usr/bin/git
```

## vi

`i` 开始输入

`:wq!`  保存并退出

`vi filename`  用 vi 打开一个文件

`esc`然后 `ctrl+z`可以退出编辑并关闭vi编辑器

## 压缩文件操作

`tar -C /usr/local -xzf node-v0.10.34-linux-x86.tar.gz` 把后面的压缩文件解压缩到目录`/usr/local`下


## 用户

```
/* 创建新用户 */
root@iZ23jp8wbg8Z:/tmp# adduser wzl
Adding user `wzl' ...
Adding new group `wzl' (1000) ...
Adding new user `wzl' (1000) with group `wzl' ...
Creating home directory `/home/wzl' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

创建完一个用户后，会在`/home`目录下以该用户名自动创建一个目录。

`su wzl`  切换为用户`wzl`

`exit`，如果由root用户切换为wzl后再执行`exit`，则回到root用户下

`passwd` 进入修改自己的密码的模式

## 系统资源与监控

`ps aux`  查看系统的所有进程(参数说明 a: 与Terminal无关；u: 有效用户相关的进程；x：列出完整信息，通常与参数a一起使用。)

`ps aux | grep node`  只查看与`node`有关的进程

`free -m -t`  以MB为单位（`-m`），显示所有可用内存（`-t`），然后打印出内存使用情况

`df -hl`  查看电脑的磁盘等信息

`lscpu`  查看电脑的CPU信息，可以据此了解CPU核心数、主频等关键信息

`top`  可以实时输出系统的CPU、内存使用情况

## 权限管理

`chown [选项] [所有者用户][:[组名]] 文件`  把某文件的所有者改为某某用户，以及归到某组下

`chmod 744 [filename]`  把一个文件的权限改为 `rwxrwxr--`

`chmod` 的意思是 `change mode`，即改变文件的状态。`chmod` 最简单的使用语法是 `chmod mode file`，其中 `mode` 是权限设定字串，格式为 `[ugoa...][[+-=][rwxX]`。

`chmod u+x [filename]`，`u` 表示该文件的拥有者，`+` 表示增加权限，`x` 表示可执行。

## 网络操作

### `lsof`命令

通过list open file命令可以查看到当前打开文件，在linux中所有事物都是以文件形式存在，包括网络连接及硬件设备。下面这个命令会列出所有占用了 8080 端口TCP连接的进程：

```
lsof -i:8080
```

`-i` 参数是用于过滤打开的文件的 `Internet address` 匹配其后面数字的。如果使用了 `-i4`，则只过滤 IPV4 的文件；`-i6` 则只查找 IPV6 的文件。

## 环境

`set` 可以查看当前会话中的所有环境信息
