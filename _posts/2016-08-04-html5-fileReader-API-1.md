---
title: HTML5 fileReader API (1)
date: 2016-08-04 22:28:02
tags: 
  - HTML5
  - fileReader
---

本文介绍在使用 HTML5 fileReader API 之 readAsText 的基础用法及常见的问题与解决方案。

<!-- more -->

## 基本使用

## 第二次选择同一个文件时不触发 change 事件的问题

## 自定义样式

原生的 `<input type="file">` 样式是这样子的：

<input type="file">

不能获得较好的交互体验。通常的解决方案是在其外面包裹一层自定义样式了的标签，然后将该 `input` 的尺寸设置为与其父元素一致、再设为透明的，就可以实现较好的自定义样式。例如：

```
<!-- html -->
<label class="input-file">
  <input type="file" name="">
</label>

# CSS
.input-file {
  display: inline-block;
  position: relative;
  border: 1px solid #ccc;
}

.input-file input[type="file"] {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
}
```

## 浏览器兼容性

### input type="file"

在 caniuse.com 里面搜索 `input-file` 可以获得浏览器们对 `input` 元素的 `type="file"` 属性的支持度。目前而言，IE 系列浏览器是从 IE10 才开始支持。Android 4.x 及更低版本的原生安卓浏览器也不支持此功能。不过，在 Android 5.x 下的 the Chrome browser 似乎是支持此属性的。

### fileReader API

从 caniuse.com 给出的兼容性数据来看（市场份额已导入了中国区的数据），IE 系列浏览器对 fileReader 的支持，仅从 IE10 起比较乐观。其他各主流浏览器则对此 API 支持度非常好。

<img src="/images/2016/08/filereader-api-compatible.png" />

### opacity

上面进行自定义样式时用到的 opacity 属性的浏览器兼容性是 IE8+ 以及各主流浏览器。可以非常放心地使用。


