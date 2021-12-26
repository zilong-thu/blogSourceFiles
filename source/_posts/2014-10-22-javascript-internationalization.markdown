---
title: 前端页面国际化解决方案小记
date: 2014-10-22 23:37:54
tags: i18n
keywords: javascript internationalization, jquery-lang.js
categories: JavaScript
sidebar: false
---
###方案1：jquery-lang.js
jquery-lang.js项目的地址为：  <a href="https://github.com/coolbloke1324/jquery-lang-js" target="_blank">https://github.com/coolbloke1324/jquery-lang-js</a>

<!-- more -->

这是一个基于jQuery的在客户端实现国际化多语言切换的插件。使用比较方便。前段时间在工作中使用了这个插件。

插件目录如下：

```
jquery-lang-js
  |-- jquery-lang.js
  |-- jquery-cookie.js
  |-- langpack
        |-- ch_nonDynamic.js
        |-- ch.json
```

在`ch.json`或者`ch_nonDynamic.js`文件中定义语言包，HTML中相应的所有短语添加`lang="en"`属性，如下所示：

```html
<span lang="en">Translate me</span>
```

在确保加载完相应资源后，执行下列语句，初始化当前语言（这里采用的是预先下载语言包的方式，也可以按需加载语言包，后者更为推荐）：

```javascript
window.lang = new Lang('en');
```

切换语言的方法，从英文（en）切换到中文（ch）：

```html
<a href="#lang-en" onclick="window.lang.change('ch'); return false;">Switch to Chinese</a>
```

###方案2：Format.JS

Format.JS项目网站：  <a href="http://formatjs.io/" target="_blank">http://formatjs.io/</a>

jquery-lang.js只是对字符串进行逐一替换，但Format.JS的功能则更为强大：除了翻译常规字符串，它还可以根据不同的语言，对日期、时间、数字等数值类型或字符串进行相应的格式化。

Format.JS既可以运行在浏览器端，也提供对Node.js环境下的支持。

【待有机会再继续研究】