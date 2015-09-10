---
title: Ubuntu下更改MySQL5.5字符集为UTF-8的过程小记
sidebar: false
date: 2015-06-27 16:39:13
tags: MySQL
categories: MySQL
---

在阿里云上部署完运行环境之后（部署参见[NodeJS+Express+MySQL开发小记(2):服务器部署](http://borninsummer.com/2015/06/17/notes-on-developing-nodejs-webapp/)），运行了几天，发现有张表的字符集默认成了 `Latin1`，导致中文只显示问号。

先趁此机会尝试安装MySQL5.6，省得以后再升级，但是折腾了一上午，未遂。权衡了一下，还是去修改当前的MySQL5.5的默认字符集得了。

<!-- more -->

## 修改之前
### 查看当前MySQL版本

```
$ mysql -V
mysql  Ver 14.14 Distrib 5.5.43, for debian-linux-gnu (x86_64) using readline 6.3
```

### 查看数据库字符集
进入MySQL之后，运行语句：

```
mysql> show variables like 'char%';
 
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```

这样可以看到MySQL所使用的字符集。

## 开始更改设置
### 查看当前MySQL所使用的配置文件的顺序
```
$ mysql --help | grep Default -A 1
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf
```
### 修改配置文件
第一次安装MySQL，`/etc/my.cnf`文件应该是不存在的。所以打开`/etc/mysql/my.cnf`：

```
vi /etc/mysql/my.cnf
```

在以下3个部分都做相应改动：
（1）在[client]字段里加入default-character-set=utf8，如下：

```
[client]
port = 3306
socket = /var/lib/mysql/mysql.sock
default-character-set=utf8
```

（2）在[mysqld]字段里加入character-set-server=utf8，如下：

```
[mysqld]
port = 3306
socket = /var/lib/mysql/mysql.sock
character-set-server=utf8
```

也可以这样改：
```
[mysqld]
collation-server = utf8_unicode_ci
init-connect=’SET NAMES utf8′
character-set-server = utf8
```

（3）在[mysql]字段里加入default-character-set=utf8，如下：

```
[mysql]
no-auto-rehash
default-character-set=utf8
```

### 重启MySQL

```
sudo service mysql restart
```

### 查看是否生效

```
mysql> show variables like 'char%';

+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```

## 如果出错了

如果重启失败了，那么可以到`/var/log/mysql/error.log`文件中查看输出的启动错误。

修改后，运行`sudo service mysql start`来启动服务。

## 表的字符集
修改了MySQL的默认字符设置，并不能更改已经生效的字符设置。如果某张表的字符仍是乱码，那么说明它的字符集仍然为Latin1。

可以这样查看某张表的建表情况：

```
SHOW CREATE TABLE table_name;
 
.....省略一大堆..() ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=latin1
```

所以最佳实践是：

__在建表语句中，显式地声明该表所要使用的字符集__

## 参考资料

+ [（原创）Linux下MySQL 5.5的修改字符集编码为UTF8（彻底解决中文乱码问题）](http://www.ha97.com/5359.html)
+ [Change MySQL default character set to UTF-8 in my.cnf?](http://stackoverflow.com/questions/3513773/change-mysql-default-character-set-to-utf-8-in-my-cnf)