---
title: 一个自定制的弹出-确认框插件
date: 2014-11-17 19:54:15
tags: JavaScript
categories: JavaScript
sidebar: false
---
这里记录几段开发一个要求输入6位PIN码、验证输入有效性、返回该PIN码的弹出确认框的代码。也提供了纯JS插件开发的一种范式（凭自己的感觉与目前对JS的理解来做的）。也许有不足之处，仅供参考。

设计时对自己提的要求有：

1. 不依赖任何JS库，使用纯JS。
2. 不使用样式表。虽然不是好习惯，但是对于小插件，只引用一个脚本便得到想要的效果，足矣。
3. 保留一定的跨浏览器能力。

<!-- more -->

```
/*
 * 说明 @wzl
 * 本插件最后会提供一个对象，可以挂载到window下或者其他对象下
 * 本例中，该对象以window.popConfirm为入口访问
 */

window.popConfirm = (function(){
	var doc = document;

	var _pop = {};

/* 定义EventUtil对象，用于提供对事件相关的统一处理方法 */
	var EventUtil = {};
	EventUtil.getEvent = function (event) {
	    return event? event : window.event;
	};
	EventUtil.getTarget = function(event){
		return event.target || event.srcElement;
	};
	EventUtil.preventDefault = function(event){
		if(event.preventDefault){
			event.preventDefault();
		}else{
			event.returnValue = false;
		}
	};
	EventUtil.stopPropagation = function(event){
		if (event.stopPropagation) {
			event.stopPropagation();
		}else{
			event.cancelBubble = true;
		}
	};
	EventUtil.addHandler = function(element, type, handler){
		if (element.addEventListener) {
			element.addEventListener(type, handler, false);
		} else if (element.attachEvent){
			element.attachEvent('on'+type, handler);
		} else {
			element['on'+type] = handler;
		}
	};


/* UI部件的生成与样式 */
	var domPopContainer = doc.createElement('div');
	domPopContainer.style.cssText = 'display:none; z-index:8000; position:fixed; top:0; right: 0; bottom: 0; left: 0; background: rgba(0, 0, 0, 0.5);transition: display 0.3s;';
	domPopContainer.name = 'popConfirm';
	doc.body.appendChild(domPopContainer);

	var text_normal = '<span style="color: #666;">PIN由6位数字组成</span>';
	var text_warning = '请输入正确的6位数字PIN码';
	var divstr = '<div name="confirmForm" style="margin: 200px auto;width:320px; height: 150px;font-size: 16px; line-height: 1; border-radius: 5px;background-color: #fff; padding: 15px;box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);">'+
				 	'<div style="text-align: center;padding-top:13px;"><label for="_pop-input-PIN" style="width:40px;padding-right:10px;"> PIN </label>'+
				 		'<input id="_pop-input-PIN" type="password" maxlength="6" style="width: 210px; padding: 5px;border-radius: 3px;border: 1px solid #ccc;"/></div>'+
				 	'<div id="_pop-div-hint" style="height:24px;padding:10px 0 4px 62px;font-size:12px;">'+ text_normal +'</div>'+
				 	'<div style="text-align: center;padding-top:15px;"><button name="confirmButton" style="height: 36px; width: 90px; border-radius:5px; background-color: #5cb85c; border: 1px solid #4cae4c;">确定</button></div>'+
	             '</div>';
	domPopContainer.innerHTML = divstr;
	var domInput = doc.getElementById('_pop-input-PIN');
	var domHint = doc.getElementById('_pop-div-hint');
	

/* 下面是对象的方法 */
	_pop.show = function(){
		domPopContainer.style.setProperty('display', 'block');
	};
	_pop.hide = function(){
		domPopContainer.style.setProperty('display', 'none');
	};
	_pop.showHint = function(html){
		domHint.innerHTML = html || '';
	};

	/* 输入的pin是个字符串 */
	_pop.validatePIN = function(pin){
		if (pin.length!==6) {
			_pop.showHint('<span style="color: #f00;">不足6个数字！</span>');
			return false;
		}
		var pattern=/^[0-9]{1,20}$/
		var isAllNumber = pattern.test(pin);
		if(!isAllNumber){
			_pop.showHint('<span style="color: #f00;">必须全部是数字！</span>');
			return false;
		}
		return true;
	};
	_pop.getPIN = function(){
		var val = domInput.value;
		_pop.PIN = val;
		return val;
	};


/* 对部件进行事件委托 */
	var onConfirmBtnClick = function(){
		var pin = _pop.getPIN();
		if(_pop.validatePIN(pin)){
			_pop.hide();
		}
	};

	EventUtil.addHandler(domPopContainer, 'click', function(event){
		event = EventUtil.getEvent(event);
		var target = EventUtil.getTarget(event);

		var name = target.name;
		switch(name){
			case 'popConfirm':
				/* 单击遮罩背景层，关闭弹出框 */
				_pop.hide();
			break;

			case 'confirmButton':
				onConfirmBtnClick();
			break;
		}
		
		EventUtil.stopPropagation(event);
	});

	EventUtil.addHandler(domInput, 'focus', function(event){
		_pop.showHint(text_normal);
	});

	EventUtil.addHandler(domInput, 'keydown', function(event){
		event = EventUtil.getEvent(event);
		if (event.keyCode == 13) {
			onConfirmBtnClick();
		}
	});

	return _pop;
})();
```