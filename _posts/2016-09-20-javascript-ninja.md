---
title: JavaScript Ninja
date: 2016-09-20 01:07:20
tags:
---

<img src="/images/2016/09/s28313380.jpg" style="width: 180px;" alt="《JavaScript忍者秘籍》封面">

书名《JavaScript忍者秘籍》，作者呢是大名鼎鼎的 jQuery 的创作者。这本书里介绍了各种“忍者级”JS用法，收益颇丰。

<!-- more -->

### 第3章 函数是根基


函数的 name 属性，有别于函数表达式的变量名，它是函数声明时指定的。


### 第4章 挥舞函数


函数名是一个有趣的概念，它的本质是 token，与变量名、对象属性名一样，都有各自的可见范围。

函数声明可以使得该函数在其所在的词法作用域内在任意处访问到。

函数表达式里，如果 function 关键字后面带有函数名，那么该函数名字只能被自己的函数体内访问到，外部都不可见。

例如：

```
var a = function b() {
  console.log(b.name);
}; 

a(); // b

b(); // Uncaught ReferenceError: b is not defined
```

而且函数名是一个优先级比较弱的标识符，函数的形参名会在函数体内覆盖函数名：

```
var a = function b(b) {
  console.log(b.name);
};
a(); // Uncaught TypeError: Cannot read property 'name' of undefined
```

而在将对象的属性指向一个函数时，如果将函数进行命名，那么其行为与函数表达式一样。这样的函数被称为内联命名函数。

72页的一段代码非常有趣，对象的方法可以调用数组原型方法，例如 Array.prototype.push.call(this, objectB)，然后如果这个对象有个 length 属性，那么这个原型方法呢就会将 length 值加 1，并且给对象添加一个数字属性，对象通过 [index] 访问这个数字属性，就可以访问到刚刚添加的对象 objectB。

#### 4.4 函数重载方式

> 重载函数是函数的一种特殊情况，为方便使用，C++允许在同一范围中声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同，也就是说用同一个运算符完成不同的运算功能。这就是重载函数。重载函数常用来实现功能类似而所处理的数据类型不同的问题。
> 
> ——来自百度百科

这本书给出的 JS 实现函数重载的技术与C++不同，但是思路是一样的：根据形参来、直观地重载；充分利用闭包来保存函数链。

```
/**
 * 用于给对象添加重载方法的方法
 * @param {[type]}   object [description]
 * @param {[type]}   name   [description]
 * @param {Function} fn     [description]
 */
function addMethod(object, name, fn) {
  var old = object[name];
  object[name] = function() {
    if (fn.length === arguments.length) {
      return fn.apply(this, arguments);
    } else if (typeof old === 'function') {
      return old.apply(this, arguments);
    }
  };
}

/**
 * 定义一个测试对象
 */
var ninjas = {
  values: ['a', 'b', 'c']
};

/**
 * 第一个是不带任何参数的方法
 */
addMethod(ninjas, 'find', function() {
  return this.values;
});

/**
 * 第二个方法带有一个字符串参数
 */
addMethod(ninjas, 'find', function(str) {
  return this.values.filter(item => (item === str));
});

console.log(ninjas.find());  // ["a", "b", "c"]

console.log(ninjas.find('c'));  // [c"]

```


### 第8章 驯服线程和定时器

同一个 interval 处理程序的多个实例不能同时进行排队。因此，setInterval 的有些回调可能就被废弃掉了。

> 减少同时使用的定时器的数量，将有助于解决这种问题（卡顿），这就是为什么所有现代动画引擎都使用一种称为中央定时器控制（central timer control）的技术。

一个完整的中央定时器控制示例代码：

```
<!DOCTYPE html>
<html>
<head>
  <title>test timer control</title>
  <style type="text/css">
    #box {
      position: relative;
      border: 1px solid #999;
      display: inline-block;
      height: 100px;
      width: 100px;
    }
  </style>
</head>
<body>
<div id="box"></div>
</body>
</html>

<script type="text/javascript">
var timers = {
    timerID: 0,
    timers: [],

    add: function(fn) { 
        this.timers.push(fn);
    },

    start: function() {
        if(this.timerID) return;
        (function runNext() {
            if(timers.timers.length > 0) {
               for (var i = 0; i < timers.timers.length; i++) {
                 if(timers.timers[i]() === false) {
                   timers.timers.splice(i,1);
                   i--;
                 }
              } 

          timers.timerID = setTimeout(runNext, 0);
       }
     })();
    },

    stop: function() {
        clearTimeout(this.timerID);
        this.timerID = 0;
    }
};

var box = document.getElementById("box"), x = 0, y = 20;
timers.add(function() {
    box.style.left = x + "px";
    if(++x > 50) return false;
});

timers.add(function() { 
    box.style.top = y + "px";
    y += 1;
    if (y > 120) return false;
});

timers.start();
</script>
```


