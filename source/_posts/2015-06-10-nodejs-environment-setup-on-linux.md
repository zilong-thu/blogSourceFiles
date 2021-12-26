---
title: Linux下配置NodeJS环境
sidebar: false
date: 2015-06-10 09:49:02
tags: 
- NodeJS
- Linux
categories: NodeJS
---

记录在Linux下通过命令行来配置NodeJS环境的点点滴滴。比较基础。

计算机系统：Ubuntu 14.04 LTS 64bit

<!-- more -->
## Install Manually with Binary Package

### wget下载NodeJS binary 包
在　`/home/username/mydownloads/`目录下，通过`wget`来下载NodeJS of Linux Binaries (.tar.gz)，当然，前提是自己得知道该软件的URL。

```
wget http://nodejs.org/dist/v0.12.4/node-v0.12.4-linux-x64.tar.gz
```
如果网络状况不佳，则可以再次进入该目录，添加 -c 参数来实现断点续下：

```
wget -c http://nodejs.org/dist/v0.12.4/node-v0.12.4-linux-x64.tar.gz
```

以`tar.gz`为扩展名的是一种压缩文件，在Linux和OSX下常见，Linux和OSX都可以直接解压使用这种压缩文件。

+ [wget命令参考](http://man.linuxde.net/wget)

### 解压缩

```
sudo tar -C /usr/local --strip-components 1 -xzf node-v0.12.4-linux-x64.tar.gz
```

即可在`/home/username/`下得到目录名为`node-v0.12.4-linux-x64`的解压后的文件夹。方便辨识起见，将这个解压后的目录重命名为`node`：

```
mv node-v0.12.4-linux-x64 node
```

这里使用`wget`的方法是自己想到的，本以为很笨拙，后来继续搜索，发现这也是别人提到过的可行方案之一：

+ [How to Install Node.js on Ubuntu 14.04](http://www.hostingadvice.com/how-to/install-nodejs-ubuntu-14-04/) 

其介绍的方法略有不同，从效果来看，这个方法要好一些。

```
/* 查看是否安装成功 */
ls -l /usr/local/bin/node
ls -l /usr/local/bin/npm
```

## Install via apt-get

可以通过Ubuntu的Package Manager下载：

```
sudo apt-get install nodejs
sudo apt-get install npm
```

但这样获得的NodeJS版本目前也就到0.10.~，很多新特性都用不了，例如`promise`。期待Unbutu将更新的NodeJS放入其软件中心的那一天。

+ [Installing Node.js via package manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager#debian-and-ubuntu-based-linux-distributions)
+ [Set Node.js to $PATH (Ubuntu 12.04)](http://stackoverflow.com/questions/13203335/set-node-js-to-path-ubuntu-12-04)

## Uninstall an application

> Enter the command "sudo apt-get remove program_name" if the program was installed from the command line via the apt utility. This command only removes the program while leaving the configuration files behind. If you want to totally remove the program and all of its files, use the command "apt-get --purge remove program_name"

```
sudo apt-get  --purge  remove  program_name
```

引用一段《鸟哥的Linux私房菜-基础学习篇（第三版）》里面的几句话，来说明变量、目录的皮毛知识：

> page 158
> 网络文件常常提到类似`./run.sh`的数据，这个命令的意义是什么？
> 答：由于命令的执行需要变量的支持，若你的执行文件放置在本目录，并且本目录并非正规的执行文件目录（`/bin`、`/usr/bin`等为正规），此时需要执行命令就得要严格指定该执行文件。`./`代表“本目录”的意思，所以`./run.sh`代表执行本目录下名为`run.sh`的文件。
