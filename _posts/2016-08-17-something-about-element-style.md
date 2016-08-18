---
title: 关于 DOM Element style 属性的一件小事
date: 2016-08-17 01:11:03
tags:
  - HTML
  - JavaScript
---


这几日写了个小插件（[仓库地址](https://github.com/zilong-thu/toast2.js)），因为对 DOM element 的 style 属性的使用不当，导致这个插件有点问题。具体来说，就是无法通过 `element.style = 'display: none'` 这种写法来隐藏元素。

**因为DOM 标准规定 HTMLElement 的 `style` 属性是只读的。**

<!-- more -->

### Element.style 是只读的！

> Note that styles should NOT be set by assigning a string directly to the style property (as in elt.style = "color: blue;" ), since it is considered read-only (even though Firefox(Gecko), Chrome and Opera allow it) because the style attribute returns a  CSSStyleDeclaration object which is also read-only.  However, the style property's own properties CAN be used to set styles.  Further, it is easier to use the individual styling-properties of the style property (as in elt.style.color = '...' ) than to use elt.style.cssText = '...' or elt.setAttribute('style', '...') , unless you wish to set the complete style in a single statement, since using the style properties will not overwrite other CSS properties that may already be set in the style attribute.

在插件的实现中，希望通过下面的代码来隐藏元素：

```
document.getElementById(GLOBAL_ERROR_STACK_ID).style = 'display: none';
```

在 Chrome、Firefox 浏览器下是 OK 的。但是在 Safari 下，以及手机的多个浏览器中，都不会生效。而且，如果该插件内部声明了启用严格模式，那么也会在设置这个 style 时提示你尝试给一个只读属性赋值。

解决办法呢，当然就是使用标准技术：

```
var ele = document.getElementById(GLOBAL_ERROR_STACK_ID);
ele.setAttribute('style', 'display: none');

// 或者
ele.style.display = 'none';

// 或者
ele.style.cssText = 'display: none';
```

上面的三种技术，是在任何浏览器下都可以将元素隐藏的。


### Element.classList

与 style 类似，classList 也是只读的：

> The Element.classList is a read-only property which returns a live DOMTokenList collection of the class attributes of the element.
> 
> Using classList is a convenient alternative to accessing an element's list of classes as a space-delimited string via element.className.

不过 classList 拥有若干方法，可以方便地操作元素样式名列表（add、remove、toggle 等等）


### Element.className

className 则既是 getter 又是 setter：

> **className gets and sets the value of the class attribute of the specified element.**



### 题外话

话说自去年12月份加入美团猫眼电影，前端业务全部都是用 React 来写。React 对于内联样式的写法是这样的：

```
<Button style={{color: 'red', marginLeft: '15px'}} />
```

在此猜测，React 的内联样式属性就是通过修改元素的 style 属性的具体子属性来实现的。


### 参考

1. [style | MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style)
