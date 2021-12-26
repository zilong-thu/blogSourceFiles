---
title: 在触摸设备上比较触摸事件与点击事件
date: 2014-11-24 23:48:48
tags: HTML5
categories: HTML5
sidebar: false
---
在触摸设备上，touch事件从手指刚碰到屏幕就会触发，而click事件则要晚一会儿才被触发。触发顺序如下：

1. touchstart
2. mouseover
3. mousemove（一次）
4. mousedown
5. mouseup
6. click
7. touchend

所以，如果想提升web在触摸设备下的用户体验，让用户觉得响应非常迅速，应该对页面的触摸事件进行事件处理程序的注册，而不应再关注click事件。

<!-- more -->

只注册了click事件处理程序的按钮：

<button id="btn-click-only" type="button" class="btn btn-success">Click Me</button>
<div class="btn-click-only"></div>

注册了touchstart事件处理程序的按钮：

<button id="btn-touch" type="button" class="btn btn-info">touch me</button>
<div class="btn-touch"></div>

<script>
window.onload = function(){
	var $btnClick = $('#btn-click-only');
	var $btnTouch = $('#btn-touch');
	
	var countClick = 0,
		countTouch = 0;

	$btnClick.click(function(event){
		++countClick;
		var str = '<div class="alert alert-warning alert-dismissible fade in" role="alert">'+
		'<button type="button" class="close" data-dismiss="alert"><span aria-hidden="true">×</span><span class="sr-only">Close</span></button>'+
		'<strong>OK, you clicked me '+ countClick +' times.</strong></div>';
		$('div.btn-click-only').html(str);
	});

	$btnTouch.bind('touchstart', function(event){
		++countTouch;
		var str = '<div class="alert alert-warning alert-dismissible fade in" role="alert">'+
		'<button type="button" class="close" data-dismiss="alert"><span aria-hidden="true">×</span><span class="sr-only">Close</span></button>'+
		'<strong>OK, you touched me '+ countTouch +' times.</strong></div>';
		$('div.btn-touch').html(str);
	});

};
</script>