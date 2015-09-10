---
title: 【翻译】你或许不知道的关于Promise的六件事
sidebar: false
date: 2015-06-15 22:22:26
tags: Promise
categories: JavaScript
---

原文链接：[Six Things You Might Not Know About Promises | sitepoint](http://www.sitepoint.com/six-things-might-know-promises/)（2014年1月27日）。

<!-- more -->

翻译说明：
+ 我讨厌`alert`，所以把作者的`alert`代码用`console.log`代替了
+ 部分不会翻译的，直接上英文了

============ 以下为译文 ======================

Promise的概念很简单，即使你不曾用过，也很可能读到过跟它又关的介绍。它是一个非常有价值的结构，可以使得异步执行的代码以更易读的结构呈现出来，而不是乱七八糟的匿名函数嵌套。本文介绍了你可能不知道的有关Promise的六件事。

在深入下去之前，我们先简单地回顾一下JavaScript Promises是什么样子的：

```
var p = new Promise(function(resolve, reject) {
  resolve("hello world");
});
 
p.then(function(str) {
  console.log(str);
});
```

##`then()`返回的是一个新的Promise
思考下面的两段代码有何区别？

```
/* 代码段 A */
var p = new Promise(/*...*/);
p.then(func1);
p.then(func2);
```

```
/* 代码段 B */
var p = new Promise(/*...*/);
p.then(func1)
 .then(func2);
```

如果你认为这两段代码执行结果是一样的，那就说明在你看来，promises不过是个回调函数组成的一位数组而已。然而事实并非如此。每次调用`then()`方法，它都会返回一个新的Promise对象。所以，在代码段A中，如果`func1()`抛出了一个异常，`func2()`仍然是不受影响地执行的。

但在代码段B中，如果`func1()`抛出了一个异常，那么`func2()`将不会执行，因为第一次调用`then()`方法返回了一个新的Promise对象，该Promise的状态是“拒绝”（rejected）。结果就是`func2()`被忽视。

所以结论就是：promises can fork into multiple paths like a complex flow chart.

##回到函数应该传递结果
下面的代码输出的是什么？

```
var p = new Promise(function(resolve, reject) {
  resolve("hello world");
});
 
p.then(function(str) {})
.then(function(str) {
  console.log(str);
});
```