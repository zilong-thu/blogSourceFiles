---
title: Acorn.js 原理与应用
date: 2018-11-06 12:48:00
tags:
categories: JavaScript
---

[Acorn.js](https://github.com/acornjs/acorn) 是个好工具，可以做词法分析和语法分析，生成 AST，配合 acorn-walk 以及 escodegen，可以完成许多高级的事情。在工作中用它解决了一些问题。顺便就做了个分享，关于 Acorn.js 的原理与应用。下面是分享的 PPT，在此备份一下。

**[PPT 下载](/files/2018/11/acorn.js-intro.pdf)**


PPT 中部分代码示例：[learn-acorn.js | zilong-thu's github](https://github.com/zilong-thu/learn-acorn.js)。

有些示例是源码阅读，这个大家直接下载 acorn.js 的源码来看就好了。另外有部分代码是公司的项目，因此不能放出来，见谅~~

在我的电子书[《Web前端工程化与组件化开发实践》](https://borninsummer.com/Practice-in-Front-End-Engineering-and-Components-Development/) 中，有一章专门对 JS 的解析器进行介绍，感兴趣的朋友可以移步：[JavaScript 解析器](https://borninsummer.com/Practice-in-Front-End-Engineering-and-Components-Development/part-2/chapter-3-js-parsers/1-js-parser.html)。
