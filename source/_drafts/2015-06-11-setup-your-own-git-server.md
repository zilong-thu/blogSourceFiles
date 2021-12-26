---
title: 搭建自己的Git服务器并实现自动部署
sidebar: false
date: 2015-06-11 14:15:02
tags: 
- 运维
- git
categories: 运维
---

记录Git服务器/自动部署代码的过程。

<!-- more -->


##参考资料

+ [搭建Git服务器](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)
+ Git Book [Pro Git 中文版(https://git-scm.com/book/zh/v2)
+ [服务器上的 Git | Pro Git 1st Edition](https://github.com/progit/progit/blob/master/zh/04-git-server/01-chapter4.markdown)
+ [轻松打造自己的Git Server](http://blog.webfuns.net/archives/1746.html)
+ [如何创建你自己的Git服务器](http://blog.jobbole.com/60505/)

许多文章都提到通过创建用户组并赋予其特定权限，来实现远程git服务器的搭建，例如：

+ [Linux 创建用户，分配权限](http://fuwenchao.blog.51cto.com/6008712/1392525)
