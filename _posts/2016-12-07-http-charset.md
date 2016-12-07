---
title: HTTP 头的中文字符处理案例
date: 2016-12-07 18:05:00
tags: HTTP
categories: HTTP
---

简单记录一下工作中遇到的两个与HTTP&中文字符相关的问题及解决方案。

## 下载文件名含中文而浏览器解析为“乱码”

如果文件名含中文，那么在用 Chrome、Safari 时，浏览器可能会自动对下载文件进行一次 decodeURIComponent()，但是严格按照 W3C 标准实现的 FireFox 并非如此。

<!-- more -->