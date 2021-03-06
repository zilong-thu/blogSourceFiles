---
title: 小结：最近一月学到的东西
date: 2015-10-02 23:02:22
tags: WebFE
categories: 总结
---

最近一月（主要是9月份），通过若干项目，学到了一些新技能。

月末又读了几篇学习方法理论相关的博文，加深、印证了自己的观念。

对于大多数的我们来说，可以认为“世无天才”，唯有“勤奋”二字，可以将人与人区分开来。绝大部分时候，“我们的努力程度之低，根本轮不到拼天分”。至于学习方法，要理解体会这个规律：

> 这个世界的知识，对于一个人来说，有三层：
> 最内一层是舒适区，在这里面的知识都是这个人掌握、运用得非常好的。就像一个学了一两年前端开发的同学A，对JS的各种特性肯定是如数家珍。
> 最外面的是新领域，叫做“恐慌区”，这里的知识对这个人来说都是陌生的，完全没有掌握甚至没有接触的。就像那个同学A（假定TA就像我一样，是自学编程，因此只会前端开发），如果让他去用JAVA写后台，通常真的会很痛苦。
> 处在新领域和舒适区之间的，就是学习区。好比如果这个同学懂JS，又要学后端开发，那么直接上手Node.js，可能比先学JAVA，然后开始做后端开发要稍微少点门槛。

<!-- more -->

之所以说“稍微少点”，是因为后端开发并不只是编程语言层面上的东西，编程语言只是服务器端开发的一个子集，理论上任何一门编程语言都可以实现一整套的服务器端逻辑，这个时候变的是语言，不变的是诸如HTTP、TCP、并发处理、页面渲染、文件分发、认证授权、会话管理、路由设计、并发处理等等。

作为一个程序员，要经常跳出自己的舒适区，去学习一些新东西。

很多新技术、新工具，通常都会提供一个“学习区”，叫做“Get started”，或者“快速入门”，或者“Hello world”。通过看这些东西，就可以知道这个是不是处在自己的学习区内。

对于程序员来讲，往自己的上下游发展，是扩展自己技能树、提升个人能力的一个好途径。例如前端去学点设计，美工去懂点CSS，前端了解一些服务器开发技能，后端开发人员学习运维知识，等等。

## Angular.js
Angular.js是个好东西。开发CRUD类型的单页面应用时务必首选此货！！！

用Angular做了一个网站的管理员后台，期间参考了目前看到的最好的Angular书籍，《精通AngularJS》，是Angular的开发团队的成员写的，极力推荐。

用的版本是`v1.*`，等`v2.*`版本正式发布出来，还是得继续跟进。

## 前后端分离开发实践

何为前后端分离？

Web开发领域有个词现在比较火：__大前端__

国内许多大公司也已经做出了相应的技术架构更改，例如淘宝，美团。

[前后端分离的思考与实践(一)](http://ued.taobao.org/blog/2014/04/full-stack-development-with-nodejs/)

[美团酒店Node全栈开发实践, 2015-06-26](http://tech.meituan.com/node-fullstack-development-practice.html)

### Node.js作为代理服务器
视图的渲染工作有时是在Node端进行，有时是浏览器通过AJAX获取JSON数据来进行。如果网站存在认证机制，那么就可以使用node.js设计一个专门的路由，作为代理服务器。例如约定所有发往`/api/`的AJAX请求（包括GET, POST, PUT, DELETE等），都交由此路由进行转发。后台服务器则只对该node.js服务器提供数据服务（通过防火墙之类的措施）。

### 模板
最开始学习Node.js，搭建一个微博网站，就是直接用EJS。也一直用到现在。因为没有见识过更好的模板，因此居然一直觉得这货很好用。

直到有一天，我发现它不支持模板继承。

拜拜，ejs。

最近的一个项目把Django的工程做了前后端分离，当时为了兼容Django的模板，就用了Nunjucks。还不错，这货也是前后端模板都可以统一的。所以，Nunjucks就进入了我的舒适区了。

从此我也成了有阅历的人了：“不支持继承的模板不是好模板”。

### 真的是前后端通用吗？
其实，有时候前后端并不是完全通用一份模板文件的。例如标签，如果一个页面有一部分是Node.js来渲染的，同时又输出了一部分包裹在`script`标签内部的浏览器端模板，那么着二者的标签一定是不一样的。大部分的模板标记系统都提供了更改标签的接口。

__重要的不是模板文件统一，而是模板语法的统一。__

然后，之所以说“有时候”，是因为可以采取这样的手段，来使浏览器与服务器端Node.js使用同一份模板文件：浏览器先加载页面，此时经过一次服务器端渲染；然后，浏览器单独发起一个AJAX请求，获取模板文件，拿到后把它查到DOM里面。这样就可以减少更改标记的麻烦。但问题肯定也存在：增加了一次HTTP请求，大多数时候可能不值得。

## 切图技能

对于没有上游工序的状况（即，没有美工给我提供稿件图），都是自己拍脑袋想出来布局、配色等等。因此对于切图、取色等技能，着实欠缺。

现在了解了切图是怎么回事。然后觉得其实也就这么回事。

工具：Photoshop CC 2015 + Cutterman

因为是在国内，也因为银子少，PS就搞破解版了。

## 前端JS脚本CMD模块化管理

在朋友的推荐下，学习了sea.js的使用。感觉的确比require.js好用多了。后面那货，太2了。对node.js比较熟悉的话，前端脚本资源果断用CMD方式加载比较好。

CMD!! CMD!!!!

## 该认真学习ES6了！
Node.js发布V4.0版本之后，ES6就不得不提上自己的学习日程了。在一些技术卓越的团队里，ES6已经在普及开来了。

一些新特性的确让人欢欣鼓舞，例如`Object.change`事件可以解决AngularJS的性能问题。而使用`let`关键字则可以声明块级作用域的变量，这样可以解决有时不得不用匿名函数来创造局部作用域的麻烦了。

## 接口测试神器之：Postman

Postman是Chrome浏览器的一个扩展应用。界面非常爽。发起各种HTTP请求也很方便。真是前后端开发的好帮手。