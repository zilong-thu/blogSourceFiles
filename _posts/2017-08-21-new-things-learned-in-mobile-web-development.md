---
title: 初涉 Mobile Web 开发
date: 2017-08-21 01:13:21
tags: Mobile Web
categories: Web-FE
---

2015年12月至今年6月，我在猫眼做的开发基本都是桌面浏览器项目、B 端产品，技术栈是 Node.js/Koa + Webpack + React.js。从今年7月份开始，趁着一个新的 C 端项目的启动，自己转而投入到移动端 web 开发中。

C 端产品与 B 端产品差异巨大。

<!-- more -->

## 服务器端渲染

页面的首屏部分最好由服务器端执行渲染。页面的其他部分则可以灵活处理。很多 AJAX 请求的响应不必是数据，可以是服务器端根据数据使用模板渲染后的 HTML 字符串，浏览器只负责执行类似 `Element.innerHTML = response.htmlStr` 这的简单视图替换或者插入。

## UI/交互体验

前端需要解决这些移动端特有的交互问题：

+ 下拉刷新/上拉无限加载（touchEvents）
+ 快速点击响应（例如 fastClick.js）
+ 元素吸顶（sticky）

即使页面效果在开发机器上通过 Chrome/Simulator 等调试通过，在移动 web 上还是可能会出现不一致。举个例子，PC 浏览器对于 `position: fixed` 的实现是完全合乎标准，而且高性能的，但是在 iOS 下面，绝对定位的元素如果在 DOM 树上是 `overflow: scroll` 的容器元素的后代节点，那么容器在滚动时，就会引起绝对定位元素不停抖动。此时就需要将绝对定位元素与滚动容器解除父子/祖先后代关系。

``` html
<style type="text/css">
.scroll-y {
  height: 100%;
  padding-top: 40px;
  overflow-x: hidden;
  overflow-y: scroll;
}
.fixed {
  position: fixed;
  height: 40px;
}
</style>

# 下面的 DOM 结构会抖动

<div class="scroll-y">
  <div class="fixed">fixed element</div>
  <div class="content">很多内容在这里</div>
</div>

# 下面的 DOM 结构不会抖动
<div class="fixed">fixed element</div>
<div class="scroll-y">
  <div class="content">很多内容在这里</div>
</div>
```


## Hybrid 环境判断

猫眼专业版的 Web 页面大部分情况下是运行在 APP 内部的。有的功能只有特定版本区间的 APP 才可以使用。所以需要 Web 开发人员根据 `User-Agent` 来判断客户端环境。应当对此进行功能封装，关注这些指标：操作系统及版本，是否运行在 APP 中，是的话 APP 的版本号。


## 前端性能

+ 图片懒加载
+ 小图片内联为 base64 格式

## 《跨终端Web》by 徐凯

> 面对这种终端碎片化的潮流，前端工程师怎么办？解决方案就是基于最重要的前端开发思想渐进增强和优雅退化得出的移动优先：一是毫无疑问绝大部分用户已经或正在成为智能设备用户，我们要为80%的目标用户服务；二是专注于核心业务需求，人的本性、业务本质和商业模式本质基本上不会随着终端改变而改变，所以相 同业务在手机、平板、桌面和电视上呈现的本质和商业模式不会有不同，小屏幕终端是我们重新思考业务本质和核心人机交互流程的机会，其挖掘出的本质会改变其 他终端；三是针对未来人机交互，现在移动设备引领人机交互的变革潮流，通过必备特性虚拟或增强现实，并逐步引入到桌面和电视等设备中。

这本书提到了一些前端工程师需要牢记并执行的准则。简述如下：客户端中的 WebView 已经成为业务中十分重要的移动浏览器……根据浏览器、操作系统、屏幕分辨率对浏览器进行分级……A级为优先级最高，A级要求测试通过所有的测试用例。**前端需要在提交测试之前，按照上限标准进行充分的自测**。

## 开发调试

移动端 Web 开发的调试比 B 端页面繁琐多了，而且花在浏览器兼容性方面的自测时间也要长许多。

调试的两大利器：

+ Charles
+ XCode Simulator

