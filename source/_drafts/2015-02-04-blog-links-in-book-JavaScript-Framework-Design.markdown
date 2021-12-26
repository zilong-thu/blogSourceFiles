---
title: 《JavaScript框架设计》书中引用到的文章链接
date: 2015-02-04 21:28:20
tags: 读书笔记
categories: JavaScript
---
记录<a href="http://book.douban.com/subject/25858070/" target="_blank">《JavaScript框架设计》</a>一书中提到的比较好的文章的URL地址。
<!-- more -->
page 31. 《编程语言伪简史》： <a href="http://james-iry.blogspot.co.at/2009/05/brief-incomplete-and-mostly-wrong.html" target="_blank">A Brief, Incomplete, and Mostly Wrong History of Programming Languages</a>

page 40. 数组洗牌算法：<a href="http://bost.ocks.org/mike/shuffle/" target="_blank">Fisher–Yates Shuffle, January 14, 2012 </a>

page 70. 用于检测浏览器事件支持的方法：<a href="http://perfectionkills.com/detecting-event-support-without-browser-sniffing/" target="_blank">Detecting event support without browser sniffing</a>

page 73. 现代浏览器的一个新API，即CSS.supports，的使用及相关的polyfill： <a href="https://github.com/termi/CSS.supports" target="_blank">github.com/termi/CSS.supports</a>

```javascript
window.CSS.supports('transition', 'color 0.4s');  /* true,  Chrome39 和 FF 35 */
window.CSS.supports('display', 'flex');  /* true,  Chrome 39 和 FF 35 */
```