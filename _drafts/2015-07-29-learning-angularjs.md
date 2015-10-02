---
title: AngularJS学习笔记
sidebar: false
date: 2015-07-29 09:34:30
tags: AngularJS
categories: JavaScript

---

## SPA
如果是从零开始，不使用任何框架来开发一个单页面应用，那么最可怕的噩梦应该就是后期的维护问题了：太多的脚本，凌乱的依赖。

> The challenge with building SPAs from scratch is there’s a lot of different issues to deal with: DOM
manipulation and history and how do you dynamically load modules and how do you deal with
promises when you make async calls and things like that.
>
> --[来源](http://fastandfluid.com/publicdownloads/AngularJSIn60MinutesIsh_DanWahlin_May2013.pdf)

大概就是说，但不使用框架（单纯使用类库例如jQuery+Requirejs也不算使用框架，因为它们不能提供封装得更完善的功能，只是强化／简化了一些操作而已，事件处理、Ajax都要自己手动进行）来开发单页面应用，会面临这些问题：

+ DOM操作
+ 浏览器浏览历史, history
+ 动态加载模块
+ 处理promise


