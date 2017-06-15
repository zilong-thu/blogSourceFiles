---
title: MySQL 基本操作笔记
date: 2017-06-15 15:33:00
tags: MySQL
categories: MySQL
---

简单记录一下 MySQL 的最基本的操作命令。

<!-- more -->

## 安装、启动


## 用户、密码、登录

`mysql -uroot`  数据库不设密码，直接登录

`mysql -uroot -p`  数据库设置了密码，那么会提示输入密码，正确的话即可登录进入


## 查看数据库、表

`show databases;`  查看已有的所有数据库

`use <database-name>;`  选择并进入某个数据库

`show tables;`  显示该数据库中的所有表

