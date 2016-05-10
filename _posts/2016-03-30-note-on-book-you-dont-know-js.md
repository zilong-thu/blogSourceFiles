---
title: 《你不知道的JavaScript》笔记&心得
date: 2016-03-30 23:40:08
tags: 
 - JavaScript
 - 读书笔记
---

<img src="https://img3.doubanio.com/lpic/s28033372.jpg" style="width: 200px; float: right; margin: 0 15px 0;" />

（持续更新）

这本书于2015年4月在国内出版，而2个月之后，ES6正式通过，成为国际标准。

ES6的许多特性也在本书中有所提到。例如块级变量声明，箭头函数，`Object.keys()`方法，`Array.prototype.find()`方法，属性名表达式，以及 `class`，等等。

个人认为，本书对 JS 的多处讲解，深度甚于《JavaScript权威指南》和《JavaScript高级程序设计》。

<!-- more -->

## 编译原理

JavaScript 引擎进行编译的步骤和传统的编译语言非常相似，在某些环节可能比预想的要复杂。

> 编译原理学了有什么用呢？
>
> 可以移步知乎：[编译原理学了有什么用？](https://www.zhihu.com/question/21755487)

### 传统编译语言的编译过程

传统编译语言编写的源码在执行之前要经历的若干个步骤，统称为“编译”。下面的步骤参考《编译原理》<sup>[3]</sup>

** ① 分词/词法分析（Tokenizing/Lexing） **

编译器的第一个步骤称为词法分析（lexical analysis）或扫描（scanning）。

** ② 解析/语法分析 **

此步骤被称为语法分析（syntax analysis），或解析（parsing）都可以。语法分析的结果是给出前面的词法分析产生的词法单元流的语法结构。一个常用的表示就是语法树（syntax tree），树中的每个内部结点表示一个运算，而该结点的子结点表示该运算的分量。

** ③ 语义分析 **

语义分析的一个重要部分是类型检查（type checking）。

** ④ 中间代码生成 **

** ⑤ 代码优化 **

** ⑥ 代码生成 **

### JavaScript的编译过程（以V8引擎为例）

变量、函数提升


## 词法作用域 vs 动态作用域

> 如果仅仅通过阅读某个语言的程序就可以确定其作用域，那么这个语言就使用了**静态作用域**，或者说词法作用域。否则这个语言就使用了**动态作用域**。
>
> ——《编译原理》第1章 page 23.

大部分编程语言的作用域规则是词法作用域，JavaScript 也不例外。词法作用域意味着作用域在词法解析阶段即已确定，不会改变。

> 词法作用域(lexical scope)等同于静态作用域(static scope)

## 函数作用域 vs 块作用域

### 函数形参默认值的作用域

ES6 允许为函数的形参指定默认值。如果参数默认值是个变量，则该变量在真正被赋值给形参时，是属于函数外部的作用域的。（参考 [2] 第73页）

```
var a = 'outer';

function foo(x = a) {
    var a = 'inner';
    console.log(x);
}

foo();  // 输出 outer
```

而下面的代码则会报错：

```
function foo(x = b) {
    var b = 'inner';  
    console.log(x);
}

foo();  // ReferenceError: b is not defined
```

## 关于 this

### 为什么要支持 `this`？

## 对象

函数是对象，但是函数对象是无法进行复制的。（存疑）


## 参考资料

1. 《你不知道的JavaScript（上卷）》,  [美] Kyle Simpson
2. 《ES6标准入门（第2版）》，阮一峰，2016
3. 《编译原理（第3版）》, Alfred V. Aho, etc.
4. 《操作系统》
