---
layout: post
title: 简析Ajax GET与POST区别
date: 2014-11-11 22:54:30
tags: Ajax
categories: JavaScript
sidebar: false
---

Ajax的get方法与post方法之间存在着形式、性能上的诸多不同。是前端工程师笔试或面试中常见的题目。考察的知识很基础。在二者中选择哪一个，也是实际开发中经常用到、需要考虑的问题。

<!-- more -->

###基本概念
GET是最常见的请求类型，最常用于向服务器查询某些信息。必要时，可以将查询字符串参数追加到URL的末尾，以便将查询条件发送给服务器。GET请求的基本语法如下所示：

```javascript
var xhr = new XMLHttpRequest();

/* 第三个参数表示是否异步。默认为true（异步） */
xhr.open('get', 'example.php?userid=001&gender=female', true); 
xhr.send(null);
```

使用频率仅次于GET的是POST请求，通常用于向服务器发送应该被保存的数据。POST请求应该把数据作为请求的主体提交，而GET请求传统上不是这样。POST请求的主体可以包含非常多的数据，而且格式不限。一个有效的POST请求的基本语法格式如下：

```javascript
var xhr = new XMLHttpRequest();
xhr.open('post', 'example.php', true);
xhr.send(somedata);
```

###传递的数据
GET方法如果要向服务器发送数据，只能显式地将参数经过URI编码后放在所请求的URL后面。因此GET方法传递的数据量受限于URL的长度。

<a href="http://www.caopeng.net/2011/11/liu_lan_qi_neng_rong_na_de_zui_chang_url_chang_du/" target="_blank">《浏览器能容纳的最长URL长度》</a>这篇文章研究了不同浏览器的URL最大长度问题。时间较久远了，也许与现状的情况不完全吻合了。

###性能对比

> 与GET请求相比，POST请求消耗的资源会更多一些。从性能角度来看，以发送相同的数据计，GET请求的速度最多可达到POST请求的两倍。
> ——《JavaScript高级程序设计（第3版）》578页

###安全性
POST请求将内容放在HTTP请求的主体（body）中进行发送，可以进行加密。而GET请求则只能以URL参数明文发送数据，因此安全性不如POST。