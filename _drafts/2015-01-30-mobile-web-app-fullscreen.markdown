---
title: Web App Fullscreen
date: 2015-01-30 22:49:13
tags: HTML5
categories: HTML5
layout: draft
---

移动端的网页全屏显示（隐藏顶部的地址栏和底部的操作栏）貌似尚无统一标准。特此研究一下。

<a href="http://touch.qunar.com" target="_blank">去哪儿网的移动版：touch.qunar.com</a>在我的小米3（MIUI 6）自带浏览器上打开时，是以全屏的形态呈现的。

观察其页面`<head>`内部的`<meta>`标签，发现有这么几个：

```html
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="full-screen" content="yes">
<meta name="browsermode" content="application">
<meta name="x5-fullscreen" content="true">
```
相应的图片：

<div id="image-to-load-01" style="width: 300px; height: 60px;padding: 20px;border: 1px solid #ccc; background-color: #4369FF; cursor: pointer;" data-src="/images/blog/2015/01/touch.qunar.jpg">点击下载图片。图片大小约130KB。</div>

未完待续。。。

<script>
window.onload = function(){
	var container = $('#image-to-load-01');
	container.click(function(event){
		container.html('下载中……');
		var url = container.data('src');
		var img = document.createElement('img');
		img.src = url;
		$(img).load(function(){
			container.replaceWith($(img));
		});

	});
};
</script>