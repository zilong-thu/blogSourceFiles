---
title: Ubuntu install and config nginx
date: 2015-10-15 13:32:08
tags: Nginx
---

包括
+ 给普通用户分配管理员权限
+ 安装nginx
+ 配置nginx
+ 多个域名指向同一个IP机器上的不同服务

<!-- more -->

## User权限
安装程序使用一个普通用户账号，但是具有管理员权限。例如用户名`user_a`，如果不具有管理员权限，那么在用`sudo`执行安装操作时常常会看到这样的错误提示：

```
user_a is not in the sudoers file.  This incident will be reported.
```

解决方法如下（参考[How do I add myself into the sudoers group?](http://askubuntu.com/questions/124166/how-do-i-add-myself-into-the-sudoers-group）：

以`root`身份登陆或者通过`su root`来提升权限，输入`visudo`，回车，会打开一个编辑器，找到这一行：

```
root ALL=(ALL) ALL
```

在下面添加一行：

```
user_a ALL=(ALL) ALL
```

`ctrl+x`， 在对话询问中输入`Y`，然后回车即可。

## 安装nginx

```
sudo apt-get update
sudo apg-get install nginx
```

## 配置nginx
