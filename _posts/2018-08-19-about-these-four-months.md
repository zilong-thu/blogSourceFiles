---
title: 最近这四个月
date: 2018-08-19 18:06:34
tags:
category:
banner: /images/2018/08/street-after-work.jpeg
---

2018 年过去了三分之二，自 4 月底来到美团出行事业部，到现在大约 4 个月。简单写写读过的书，学到的新技术，以及思考的事情。

<!-- more -->

## 读过的一些书

### 《精通JavaScript（第2版）》

这本书是 jQuery 之父 John Resig 与其他几个人合作所著。对我而言，已经没有什么亮点。的确不如《JavaScript忍者秘籍》好。

### 《代码的未来》

早在研究生阶段，就大略看了一下这本书中关于 JavaScript 的部分。今年把整本书能看懂的都看了一遍。比较有意思的是松本行弘对 CoffeeScript 的预言彻底失败。内存管理（各种垃圾回收机制）、闭包、并行计算、分布式计算、C10K、进程间通信也讲得很有意思。

TCP 通信实际上也是进程间通信的一种了，虽然是针对不同计算机上面的进程。

UNIX 管道是利用多核优势的一个好模型。

并行处理的性能提升上限由阿姆达尔定律给出。大概就是说，并行计算最终会因为总有一些计算是无法并行而达到性能极限。这就像生孩子一样，一个女人生一个孩子需要10个月，但并不意味着 10 个女人就可以在 1 个月内生完孩子。

### 《只是为了好玩 : Linux之父林纳斯自传》

豆瓣评论：Linus文笔普通，博客式自传罢了。译者的文字功底倒是不错。主角是林纳斯，地球上最牛逼的程序员~推荐所有的程序员都看一下~~

可以了解到 Linux 的诞生详情，林纳斯与 Minix 的恩恩怨怨，林纳斯与乔布斯的分歧、与微软的对峙。

### 《图解HTTP》

去年以来，自己对 HTTP 的基础知识已经掌握得不错了。读这本书的主要收获，大概就下面这几点：

+ HTTPS，大概了解了 HTTPS 的过程、非对称加密的思路。需要实践下。以及，以后有机会要再看看 HTTPS 传输过程分析。
+ HTTP 报文的结构，首部也是分类的，有只属于请求的首部，有只属于响应的首部，有请求体用的首部，还有的是通用首部。

### 《2016语录》

从 2010 年开始，每年都读的。2016年的那本，忙忘了。现在再读，有些事情都还依稀记得。特朗普的个人色彩在那时候就被抨击，而很多事情似乎那时候都预见到了。

读得很快，这本书最好的部分，其实还是国内的文化、社会、文艺相关的部分。快餐式阅读，花费总时间也不多。聊胜于无吧。

## 学到的新技术

### Vue

出行事业部主要使用 Vue 进行开发。组件化思路与 React 大同小异。数据的响应式比 React 的确方便不少。

之前写 React，现在换到 Vue 生态，阻碍非常少。

VSCode 在处理 Vue 组件文件的时候的确要比 Sublime 好一些。

### Webpack 打包结果分析工具

之前一直使用 Webpack Visualizer 插件，现在了解了其他更好的工具：Webpack Bundle Analyzer.

### 使用 localStorage 进行离线缓存

前端资源的 md5，意味着资源可以根据内容进行版本控制。可以使用 JS 实现一个完全的 JS 加载器，并且利用 localStorage（以下简称 LS） 进行离线缓存。

HTML -> 解析本页面所需的 JS 路径及版本号 -> 读取 LS 缓存 -> 判断是使用缓存还是重新获取（要考虑加载顺序、依赖关系）

LS 缓存对性能的提升是非常明显的。

### 学到了一些新的面试题

主要是与 JavaScript 相关的。函数、任务队列的设计。

### Node.js 的调试技巧

基于 Node 的 Inspect 协议，配合 Chrome 浏览器的 DevTools，可以非常方便地调试 Node 里运行的 JavaScript 代码。

### Eruda.js

学习了 Eruda.js 的源码，并且向其贡献了一个小功能：NetWork 面板支持查看通过 XMLHttpRequest.prototype.setRequestHeader 设置的 HTTP 首部。

Eruda.js 是通过重写各种对象、函数的原型来实现网页调试的。

### BigInt

了解了 BigInt 草案。写了个博文：http://borninsummer.com/2018/06/20/bigint-is-coming/。

## 关于写作

跟出版社约的书稿，目前接近 13 万字了。还是很多时候懒，以及忙，导致的进度缓慢。感觉很对不起编辑。

博客更新，貌似也……还正常吧。

今年开了个公众号，写了几篇。仅仅能够做到月更。

## 一些思考

### 关于管理

学生时代，国内一直在声称与西方发达国家差距，强调管理体系、制度、理念、文化方面的不足。但是现在，我感受到了许多改变。

在学校里，即便在清华，管理理念虽然已经比大多数国内学校好，但还是不够开放与民主。本科时代可能还好，研究生阶段就要开始变惨了。如果跟了不好的老板（导师），那可要倒霉了，这种导师通常会采取封建时代那种管理制度。

毕业后去南京一家国企待了一年半，再之后在互联网公司待了两年半。二者管理方面的差异也是非常大。国企里，你基本上见不到领导，一旦见到领导，要快速反应过来，打招呼喊领导好。互联网公司里，几乎不存在“领导”这个词，可以叫老大，某某哥，或者直呼其名，而且他们一般离你的工位不远；大家都是很扁平化的关系，层级并不是那么明显。

没有在外企呆过，但是猜测美团这样的互联网公司的管理理念，应该是比较接近国外的了吧。

对于技术管理者，我有了一个新的认识，这个岗位的最重要意义在于，首先自己足够技术牛逼，然后通过一些团队管理技巧，使得大家都尽可能变得跟自己一样厉害（这个目标永远都到不了，只能无限逼近）。

### 关于房子

国内虽然已经说了房子不是炒的是住的，但是实际上，各种高层透露出来的信息表明，房产已经严重地跟金融货币体系挂钩了。房子如今更像是投资品。

自己反正是买不起房。就算以后买得起了，也要谨慎。

### 俩人合租

女友终于毕业了，来北京工作。工作内容是她喜欢的，挺开心的。我也挺开心的。

俩人合住比一个人有意思多了。有人说说话，嘿嘿。

然后做饭的次数稍微多了一些。。。偏荤。