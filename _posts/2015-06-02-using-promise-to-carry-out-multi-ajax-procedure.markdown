---
title: 用Promise实现多个Ajax的异步流程控制
sidebar: false
date: 2015-06-02 21:04:15
tags: Promise
categories: JavaScript
---

我觊觎Promise好久了。但由于缘分未到，以及智商不够，一直没有机会在实战中用它。前段时间的 [2015 上海前端技术峰会](http://www.w3ctech.com/event/51) 中的主题分享之《ES6实战》让我有这样的感觉：ES5的好多特性都还没有完全掌握呢，ES6就来了。然后据说明年ES7就要来了……

所以说，

> 学无止境。

<!-- more -->

## Promise极简史

> Promise对象曾经以多种形式存在于很多语言中。这个词最先由C++工程师用在Xanadu项目中。该项目是Web应用项目的先驱。随后Promise被用在E编程语言中，这又激发了Python开发人员的灵感，将它实现成了Twisted框架的Deferred对象。
> ......
> 不过，Promise今天受到如此多关注的原因当然是jQuery。jQuery 1.5在2011年1月携 $.ajax 重量级重写之势，用其Promise实现了震惊无数的初次接触Promise对象的开发者。
> —— $3.1 《JavaScript异步编程》Trevor Burnham

在现在的jQuery中，deferred对象指的应该就是其自己实现的Promise。

## 背景描述

在浏览器端，有个表单，为了填写方便，两个输入框使用`<select>`元素供用户选择；此外，在编辑一个条目时（对应数据库中一行），还要将该条目的数据从服务器获取到，然后生成到表单中。

```
<form id="student-info">
    <input type="text" data-key="name">
    <select data-key="province">
        <option value="10">省份1</option>
        <option value="11">省份2</option>
    </select>
    <select data-key="university">
        <option value="1">学校1</option>
        <option value="2">学校2</option>
    </select>
</form>
```

## 几个工具
没有使用任何MVC或者MVVM框架。只是借助于jQuery、Underscore等进行构建。

从表单中获取数据：

```javascript
/*
 * selector: 表单容器的选择器
 */
util.getDataFromForm = function(selector){
    var data = {};
    var inputs = $(selector).find(*[data-key]);

    inputs.each(function(index, element){
        var value = $(element).val();
        var key = $(element).data('key');

        data[key] = value;
    });

    return data;
};
```

把数据渲染到表单中：

```javascript
/**
 * data: 模型数据
 * selector: 表单容器的选择器
 */
util.renderFormWithData = function(data, selector){
    var inputs = $(selector).find(*[data-key]);

    inputs.each(function(index, element){
        var key = $(element).data('key');
        $(element).val(data[key]);
    });
};
```

## 最初我是这样写Promise代码的
OK，对于上面这个表单，两个select元素是需要从服务器GET数据后生成的（借助于模板，在此不放代码了）。然后如果要编辑一个条目，还需要额外GET该条目的数据并赋值到表单里。

如果不使用Promise，而是只用回调写法，那么最多的情况下，需要嵌套三层。任何一层失败了，都不好办。而借助于Promise，可以大胆地像同步代码那样写：

### 版本-0.0.1

Promise异步流程控制-0.0.1

```javascript
function prepareForm(callback){
    var promise = new Promise(function(resolve, reject) {
        resolve();
    });

    promise.then(function(){
        $.ajax({
            url: '/rest/province/all',
            type: 'get',
            success: function(){
                /* render the province select element */
            }
        });
    })
    .then(function(){
        $.ajax({
            url: '/rest/university/all',
            type: 'get',
            success: function(){
                /* render the university select element */
            }
        });
    })
    .then(function(){
        if(callback){
            callback();
        }
    })
    .catch(function(error){
        console.log('error: ', error);
    });
};
```

在这里，`prepareForm`函数依然接受一个回调函数作为参数。如果是ADD操作，则回调中无须再次执行一个Ajax操作；如果是UPDATE操作，则回调函数需要对该条目进行Ajax请求，然后根据这个值去渲染表单——这时不需担心表单里的两个`select`元素尚未就绪，因为__一定__是就绪了之后才根据模型对象渲染表单的。

注意：__每次调用then都会返回一个新创建的Promise对象__。

### 版本-0.0.2
版本-0.0.1并没有很好地处理`rejected`情况。
【未完待续】

##后来我意识到Promise应该这样用
上面我误以为直接链式`then()`方法就是Promise处理异步流程的精髓了。然而大错特错。对于异步任务，重要的是保证数据的获取。这通常就需要自定义一个壳，来包装一下Ajax请求的参数、返回数据等，尤其要把返回的数据传给`resolve()`，以便在`then()`里可以使用这个数据。下面是例子。

```
/* 先定义一个返回Promise对象的Ajax过程 */
var getAjaxPromise = function(option){
    return new Promise(function(resolve, reject){
        $.ajax({
            url: option.url,
            type: 'get',
            data: option.data || '',
            success: function(data){
                resolve(data);
            },
            error: function(error){
                reject(error);
            }
        });
    });
};
 
/* 启动第1个异步任务 */
var p1 = getAjaxPromise({
    url: 'root/url/1'
});
 
p1.then(function(data1){
    /* 处理第1个异步任务的结果 */
    console.log(data1);

    /* 然后启动第2个异步任务 */
    return getAjaxPromise({
        url: 'root/url/2'
    });
})
.then(function(data2){
    /* 处理第2个异步任务的结果 */
    console.log(data2);
});
```

`then()` 方法可以链式调用，关键就是每个then都会返回一个新的Promise对象。

## Polyfill
对于IE、低版本Chrome（例如V32以前的）等目前并不支持Promise特性的浏览器，可以借助于es6-promise polyfill来保证根据ES6规范写的Promise代码仍能正常运行。

在Github里影响较大的Promise补丁是这个： [jakearchibald/es6-promise](https://github.com/jakearchibald/es6-promise)。如其自己描述的，That is __"A polyfill for ES6-style Promises"__。

可以像下面这样简单地判断浏览器是否支持原生的Promise，如果不支持，则异步地下载polyfill脚本。

```javascript
/**
 * 针对浏览器的情形
 */
(function(root){
    if(root.Promise === undefined){
        var script = document.createElement('script');
        script.src = '/js/es6-promise.min.js';
        script.type = 'text/javascript';
        document.head.appendChild(script);
    }
})(window);
```

## References
下面参考资料都比较好。

+ 《JavaScript异步编程》Trevor Burnham
+ [nodejs异步控制「co、async、Q 、『es6原生promise』、then.js、bluebird」有何优缺点？最爱哪个？哪个简单？| 知乎](http://www.zhihu.com/question/25413141)
+ [JavaScript Promise迷你书（中文版）](http://liubin.github.io/promises-book/)
+ [Promise | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
+ [Six Things You Might Not Know About Promises | sitepoint](http://www.sitepoint.com/six-things-might-know-promises/)
+ [An Overview of JavaScript Promises | sitepoint](http://www.sitepoint.com/overview-javascript-promises/)
+ [A Deeper Dive Into JavaScript Promises](http://www.sitepoint.com/deeper-dive-javascript-promises/)