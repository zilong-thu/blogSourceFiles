---
title: HTML5 fileReader API (1)
date: 2016-08-04 22:28:02
tags: 
  - HTML5
  - fileReader
---

本文介绍在使用 HTML5 fileReader API 之 readAsText 的基础用法及常见的问题与解决方案。fileReader 可以将文件内容异步地读入内存，然后提供给浏览器引擎。readAsText 方法将文件内容读取为纯文本。

<!-- more -->

## 基本使用

```
<!-- CSS -->
<style type="text/css">
.input-file {
  display: inline-block;
  position: relative;
  border: 1px solid #ccc;
  background-color: #0095dd;
  padding: 5px;
  color: #fff;
  border-radius: 5px;
  margin-top: 30px;
}

.input-file input[type="file"] {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: none;
  cursor: pointer;
}
</style>

<!-- HTML -->
<form data-role="input-file">
  <label class="input-file">
    <input type="file" name="files" onchange="handleFileInputChange(this);">选择文件
  </label>
</form>

<!-- JavaScript -->
<script>
function handleFileInputChange(event) {
  var file = event.target.files[0];

  var reader = new FileReader();

  reader.onload = function(e) {
    var content = e.target.result;
    console.log('content of the file: ', content);
  }

  reader.readAsText(file);
}
</script>
```

## 第二次选择同一个文件时不触发 change 事件的问题

解决方案就是每次读完文件，执行一次 form.reset()。改写 `reader.onload` 回调后代码如下：

```
function handleFileInputChange(event) {
  var file = event.target.files[0];

  var reader = new FileReader();

  reader.onload = function(e) {
    var content = e.target.result;
    console.log('content of the file: ', content);

    var form = document.querySelectorAll('form[data-role="input-file"]')[0];
    if (form) {
      form.reset();
    }
  }

  reader.readAsText(file);
}
```

## 自定义样式

原生的 `<input type="file">` 样式是这样子的：

<input type="file">

不能获得较好的交互体验。通常的解决方案是在其外面包裹一层自定义样式了的标签，然后将该 `input` 的尺寸设置为与其父元素一致、再设为透明的，就可以初步实现自定义样式。


示例：

<label
  class="input-file"
  style="
    display: inline-block;
    position: relative;
    border: 1px solid #ccc;
    background-color: #0095dd;
    padding: 5px;
    color: #fff;
    border-radius: 5px;"
><input
  type="file"
  style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; opacity: 0;cursor: pointer;">选择文件
</label>

可能会发现上面的 `cursor: pointer;` 在某些区域失效。那么如果将 input 的透明度去掉，那么就可以看到是原生的那个样式在作祟。而给 `input[type="file"]` 添加 `font-size: 0` 规则也未必有用，因为 file input 是浏览器保护程度较高的元素，有的浏览器下，其样式就是无法做成 pointer。而这目前是一个“众所周知的”困扰（参考 [Styling an input type=“file” button | Stackoverflow](http://stackoverflow.com/questions/572768/styling-an-input-type-file-button)）。

解决方案是把 `opacity: 0` 改为 'diaplay: none'（例如本文第一段代码那样）：

<label
  class="input-file"
  style="
    display: inline-block;
    position: relative;
    border: 1px solid #ccc;
    background-color: #0095dd;
    padding: 5px;
    color: #fff;
    border-radius: 5px;
    cursor: pointer;"
><input
  type="file"
  style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: none;">选择文件
</label>

上面的参考链接里，有人提出了他认为的 **最好的解决方案**：

> The best approach would be to have a custom label element with a **`for`** attribute attached to a hidden file input element. (The label's for attribute must match the file element's id in order for this to work).

相应的代码：

```
<label for="file-upload" class="custom-file-upload">
    Custom Upload
</label>
<input id="file-upload" type="file"/>
```


## 浏览器兼容性

### input type="file"

在 caniuse.com 里面搜索 `input-file` 可以获得浏览器们对 `input` 元素的 `type="file"` 属性的支持度。目前而言，IE 系列浏览器是从 IE10 才开始支持。Android 4.x 及更低版本的原生安卓浏览器也不支持此功能。不过，在 Android 5.x 下的 the Chrome browser 似乎是支持此属性的。

### fileReader API

从 caniuse.com 给出的兼容性数据来看（市场份额已导入了中国区的数据），IE 系列浏览器对 fileReader 的支持，仅从 IE10 起比较乐观。其他各主流浏览器则对此 API 支持度非常好。

<img src="/images/2016/08/filereader-api-compatible.png" />

### querySelectorAll

IE8部分支持：

> Partial support in IE8 is due to being limited to CSS 2.1 selectors and a small subset of CSS 3 selectors (see notes there). Additionally, it will have trouble with selectors including unrecognized tags (for example HTML5 ones).

IE9+ 及其他所有的浏览器则是全面支持此 API。

## 参考资料 & 扩展阅读

+ [FileReader | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)
+ [Styling an input type=“file” button | Stackoverflow](http://stackoverflow.com/questions/572768/styling-an-input-type-file-button)
+ 样式美化相关，最开始找到的是这篇文章：[css input[type=file] 样式美化，input上传按钮美化](http://www.haorooms.com/post/css_input_uploadmh)
+ [Web文件上传方法总结大全](http://www.techug.com/web-file-upload-method)