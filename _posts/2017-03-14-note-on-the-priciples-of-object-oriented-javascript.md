---
title: 《JavaScript面向对象精要》笔记
date: 2017-03-14 23:55:00
tags: JavaScript
categories: JavaScript
---

## 一切皆对象

> 其他编程语言用栈储存原始类型，用堆储存引用类型，JavaScript 则完全不同：它是以一个变量对象追踪变量的生存期。原始值被直接保存在变量对象内，而引用值则作为一个指针保存在变量对象内，该指针指向实际对象在内存中的存储位置。
> page 2

例如，所有的 JavaScript 对象都有一个方法 `Object.prototype.valueOf()`，该方法返回指定对象的原始值。如果我们一个简单的赋值语句：

```
var a = 1;
```

那么我们会发现变量 `a` 上面其实有很多方法（当然，大部分都是来自于其原型）。这就是因为该变量是通过变量对象进行追踪的，并不仅仅只有一个值（这也涉及一个概念，“原始封装类型”）。这也是 JavaScript “一切皆为对象”的理念。

<!-- more -->

## JavaScript 只有值传递

我们知道，JavaScript 是只有值传递的。而下面的描述则更为深入地解释了内在机理：

> 当你讲原始值赋给一个变量时，该值将被复制到变量中。也就是说，如果你使一个变量等于另一个时，每个变量有它自己的一份数据拷贝。
> ……每个含有原始值的变量使用自己的存储空间。
> page 3

## 鉴别数组

`Array.isArray()` 方法是 ES5 标准所提出的。目前的支持度已经非常好。IE9+，Chrome/Safari/FireFox 都支持此 API。

## 函数

### 函数即对象

> 函数其实就是对象。使函数不同于其他对象的决定性特点是函数存在一个被称为 `[[Call]]` 的内部属性。
> ……`[[Call]]` 是函数独有的，表明该对象可以被执行。由于仅函数拥有该属性，ECMAScript 定义 `typeof` 操作符对任何具有 `[[Call]]` 属性的对象返回 “function”。这在过去曾经导致一些问题，因为某些浏览器曾经在正则表达式中包含 `[[Call]]` 属性，导致后者被错误鉴别为函数。现在，所有浏览器行为都一致，`typeof` 不会再将正则表达式鉴别为函数了。

函数是 JavaScript 语言的核心。一大特点就是：函数即对象。所以，函数可以被传递来传递去……

page 18 里面对函数声明、函数表达式进行了对比。不过，对于一个细节，并没有挖得很深。这里简单说一下，大概是“当我们说起函数作用域时，你可曾忘记了什么？”。考虑下面的一段代码：

```
var func = 'test';

var func1 = function func(a) {
  console.log(typeof func);  // function
}

func1();

console.log(func1.name);  // func
```

一个函数字面量包含这几部分：

+ 关键字`function`
+ 空格
+ 函数名（function name），非必须
+ (形参列表)
+ {函数体}

其中，函数名、所有的形参，也都属于该函数作用域。这就是为什么在该函数内部，`func` 会屏蔽外部的变量。

### 函数重载

函数重载是一种设计目标，希望可以让一个函数具有多个签名。__函数签名__，是由函数名、参数个数、参数类型、返回值类型组成的。JavaScript 的函数其实根本没有签名，因为它对函数参数的数量与类型完全不做限制。对返回值的类型也一样，没有任何限制。所以可以说 JavaScript 函数是天然可重载的，也可以说 JavaScript 函数没有重载这个概念。


### bind

JavaScript 函数的 `call`、`apply` 方法，都只是临时指定一个函数调用对象（`this`），但 ES5 所定义的新方法 `Function.prototype.bind()` 则差异较大。

+ `bind` 方法会返回一个新函数，并不会更改原始函数任何东西
+ `bind` 不但会绑定 `this` 对象，同时也可以绑定参数，这两个被绑定后，绝大部分情况下是不可更改的
+ 只有该新函数使用 new 操作符调用时，相应的 `this` 绑定会失效，但是其他的参数则依然是绑定着的。

例如下面的代码：

```
function say(label) {
  console.log(label + ': ' + this.name);
}

var person1 = {
  name: 'person1'
};

/**
 * test 1
 */
var say1 = say.bind(person1, 'say2');

say1('say2222');  // 会输出 say2: person1，而不是 say2222: person1

/**
 * test 2
 */
var person2 = {
  name: 'person2'
};

say1.call(person2);  // 输出 say2: person1

var p3 = new say1('p3');  // 输出：say2: undefined
```

总结来说，`bind` 所产生的新函数，除非用 new 调用，是**永久**的。

## 对象

### 属性探测vs属性值探测

在实际的开发中，应当区分属性探测与属性值探测之间的区别。前者关注对象的某个属性是否存在、是否为自有（而非来自原型链），后者则关注属性的值（value）而非属性（property）本身。

`in` 操作符是为了解决“对象的属性是否可达”这个问题而存在的。它完全不管属性指向的值。而`hasOwnProperty`则忽略原型链，只探测对象的自有属性。自有属性是 JavaScript 在对象上调用其内部接口 `[[PUT]]` 方法添加上的。

### 关于访问器属性

对象的属性分为两类，第一类是最为常见的数据属性，`[[PUT]]` 方法默认创建的都是数据属性；另一类是访问器属性。访问器属性的一个例子如下。

```
var obj = {
  _name: 'WangZilong',

  get name() {
    console.log('you are getting name property.');
    return this._name;
  },

  set name(value) {
    this._name = value;
    console.log('you have set a new name.');
  }
}

console.log(obj.name);

obj.name = 'test';
console.log(obj.name);
```

如果想实现一个PUB/SUB模式，又想偷懒，那么可以使用访问器属性。访问器属性是在 ES5 里定义的，除了 IE8及以下浏览器、iOS5.1及之前浏览器不支持之外，其他环境中基本都支持访问器属性。

其实，在前端生态圈里，avalon、vue 这样的 MVC 框架，就是使用了访问器属性（以及借助 `Object.defineProperty()` 方法），来实现所需的“双向绑定”功能。


## 构造函数

> 每个对象在创建时都自动拥有一个构造函数属性，其中包含了一个指向其构造函数的引用。那些通过对象字面量形式或 `Object` 构造函数创建出来的范用对象，其构造函数属性指向 `Object`；那些通过自定义构造函数创建出来的对象，其构造函数属性指向创建它的构造函数。

```
var obj = {name: 'obj'};

obj.constructor;  // function Object() { [native code] }

obj.hasOwnProperty('constructor');  // 输出 false
```

我们来针对 `constructor` 这个关键词/属性，延伸讨论一下。在 ES6 里面，明确了关键字 `class` 的用法：定义一个“类”，该类可以被 `new` 操作符调用，生成一个实例对象。

构造函数有个小特点：如果创建新实例时，没有什么参数需要传给构造函数，那么构造函数后面是可以不带圆括号的。例如日期：

```
var a = new Date;

// 等价于：
var a = new Date();
```

但是，这样的编码是**不清晰**的！看不出来是函数调用。所以，不推荐省略圆括号。


## 原型即对象

原型本质上也是对象。**只有函数才有 `prototype` 属性。**

```
function Person(name) {
  this.name = name || '';
}

var creature = {
  constructor: Person,
  sayName: function() {
    console.log(this.name);
  }
};


Person.prototype = creature;

var p = new Person('Peter');

p.sayName();  // Peter

console.log(p instanceof Person);  // true


Person.prototype.sayAge = function() {
  if (this.age !== undefined) {
    console.log('I am ', this.age, ' years old.');
  } else {
    console.log('I do not know my age yet...');
  }
}

p.sayAge();  // I do not know my age yet...

// 这个时候，creature 对象是 `sayAge` 方法的实际拥有者
console.log(creature.hasOwnProperty('sayAge'));  // true
creature.isPrototypeOf(p);  // true
```

> 构造函数、原型对象和对象实例之间的关系，最有趣的一个方面也许就是对象实例和构造函数之间没有直接关系。不过对象实例和院线对象，以及原型对象和构造函数之间都有直接联系。
> 
> —— page 59
