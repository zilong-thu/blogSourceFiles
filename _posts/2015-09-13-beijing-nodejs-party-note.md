---
title: 北京 Node.js Party 参会录
date: 2015-09-13 13:50:48
tags: NodeJS
---

## 活动简介

地点：海淀区海淀西大街39号3层36氪 氪空间，位于中关村创业大街

时间：9月13日 星期日 下午13:00 ～ 17:00

+ Open Austack - Hain
> 一个MEAN架构下的开源项目介绍，包括背景、设计、开发过程，Austack是SaaS服务，Identity as a Service.

+ A Web Developer’s Learning from App Development - Steve
> 近些年来，Hy‎brid App逐渐流行一种快速的应用开发路径。主讲人拥有多年Web开发经验，但他将在本次活动中为大家分享自己最近一年App开发中遇到的故事和经验。

+ Node.js Based IM - Mofei
> Node.js的效率和稳定性一直受到大家的关注，尤其是在超高并发的情况下，这次我们尝试了解Node.js搭建大型IM即时通讯系统背后的“故事”，以及如何架构可拓展高可用性的即时通讯系统。

<!-- more -->

## Austack
SMS Verification: The best choice maybe. 5分钱/条短信。

活跃用户：好比是一个月内登陆过的用户，即活跃用户。

博客：[Austack introduction](http://blog.austack.com/2015/08/16/austack-nodejs-ionic-2/)

首页： [austack.com](http://austack.com/)

github：[arrking/austack-core](https://github.com/arrking/austack-core)

Austack所要传达的核心技术之一是__JSON Web Token__，一种面向授权而非面向状态的认证机制。

Jenkins作为自动化部署工具。

## Hybrid APP

《The Element of User Experience》

屏幕上实现一个基本功能，最小需求一般是22×22像素。据此可以分析出PC屏幕与手机屏幕UI呈现力的区别。

微交互。案例：支付宝、顺丰快递。是产品比较成熟后，在细节上做优化的设计思路。

## Node.js Based IM

架构很赞。很有参考价值。

![socket.io-arch](/images/2015/09/socket.io-arch.jpg)

__soket.io__非常方便。（可以考虑回家后自己搭建一个出来，两天应该就可以了）

### 性能

压力测试工具： websocket-bench。测试结果：

+ 24000每台机器维持TCP连接
+ 10000条/秒，发消息
+ 96000条/秒，群组消息？？？

### Brain
任何一台Nodejs 服务器启动的时候，与Brain通信，告诉它，自己启动了。Brain就将其放到可用Nodejs server列表里。Brain也会隔一段时间就去检查每一台“奴隶”的负载情况。

主要：分发Hash。

### Nodejs server

Nodejs server职能：

+ security
+ online/offline
+ type

消息类型，好比文本消息、语音消息、图片消息等。

### 群发消息
群发消息时的技术难点，瓶颈大概就是在数据库设计上。

方案1：向1000个人群发消息，数据库里写1000条消息。

方案2：

### MongoDB
性能有点扛不住了。


最后感谢主办方，并附上合影一张，哈哈哈。

![合影](/images/2015/09/20150913-party-photo.jpg)
