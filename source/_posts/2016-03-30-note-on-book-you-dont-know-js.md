---
title: 《你不知道的JavaScript》笔记&心得
date: 2016-03-30 23:40:08
tags: 
 - JavaScript
 - 读书笔记
---

<img src="https://img3.doubanio.com/lpic/s28033372.jpg" style="width: 200px; float: right; margin: 0 15px 0;" />

这本书于2015年4月在国内出版，而2个月之后，ES6正式通过，成为国际标准。

ES6的许多特性也在本书中有所提到。例如块级变量声明，箭头函数，`Object.keys()`方法，`Array.prototype.find()`方法，属性名表达式，以及 `class`，等等。

个人认为，本书对 JS 的多处讲解，深度甚于《JavaScript权威指南》和《JavaScript高级程序设计》。

<!-- more -->

这个主题成为我在美团的第一个圆桌分享主题。在此附上当时的幻灯片：<a href="/files/2016/javascript_scope.pdf" target="_blank">《JavaScript 作用域》</a>（一些图片比这篇博客丰富）。欢迎讨论~~


## JavaScript 的诞生

<img src="/images/2016/06/javascript-birth.jpg" style="width: 300px; float: left; margin: 10px 15px 0 15px;" />

> 1995 – Brendan Eich 读完了历史上所有在程序语言设计中曾经出现过的错误，自己又发明了一些更多的错误，然后用它们创造出了LiveScript。之后，为了紧跟Java语言的时髦潮流，它被重新命名为JavaScript。再然后，为了追随一种皮肤病的时髦潮流，这语言又被命名为ECMAScript。
> 
> —— 编程语言简史
>
> (注：Eczema，意为“湿疹”)
 

> Brendan Eich: “ECMAScript was always an unwanted trade name that sounds like a skin disease.”

## 编译原理

JavaScript 引擎进行编译的步骤和传统的编译语言非常相似，在某些环节可能比预想的要复杂。

> 编译原理学了有什么用呢？
>
> 可以移步知乎：[编译原理学了有什么用？](https://www.zhihu.com/question/21755487)

### 解析器 | 编译器 | 解释器

这本书的第4页说道：

> <p class="quote-error">尽管通常将 JavaScript 归类为“动态”或“解释执行”语言，但事实上它是一门编译语言……但与传统的编译语言不同，它不是提前编译的，编译结果也不能在分布式系统中进行移植。</p>

对于此，假如 ECMScript 标准里面规定了其实现必须是通过编译，那么这句话倒是没问题。但是，ECMScript 标准并没有规定这个事情，它只是对语言本身的一个描述，并不会去涉及其具体实现，也不关心其执行过程。因此，应当尽量<strong>避开把编程语言描述为“编译型”或者“解释性”的。<sup>[12, 13]</sup></strong>

不同的 JavaScript 实现可能有不同的编译方法。在 JavaScript 刚被发明出来时，Brendan Eich 受 Java 影响颇大，使用了字节码解释器（bytecode interpreter）对 JavaScript 解释执行<sup>[10]</sup>。而著名的 V8 引擎，在运行前先将 JavaScript 编译为机器码，而非字节码或是解释执行它，以此提升性能。<sup>[14]</sup>

回到【解析器 | 编译器 | 解释器】相关的主题，这几个名词的概念分别如下：

** 解析器 **

> 解析器是 parser，是编译器/解释器的重要组成部分，也可以用在IDE之类的地方；其主要作用是进行语法分析，提取出句子的结构。广义来说输入一般是程序的源码，输出一般是语法树（syntax tree，也叫parse tree等）或抽象语法树（abstract syntax tree，AST）。进一步剥开来，广义的解析器里一般会有扫描器（scanner，也叫tokenizer或者lexical analyzer，词法分析器），以及狭义的解析器（parser，也叫syntax analyzer，语法分析器）。扫描器的输入一般是文本，经过词法分析，输出是将文本切割为单词的流。狭义的解析器输入是单词的流，经过语法分析，输出是语法树或者精简过的AST。
>
> 参考[13]

** 解释器 **

> 解释器是 interpreter。解释器就是个黑箱，输入是源码，输出就是输入程序的执行结果，对用户来说中间没有独立的“编译”步骤。这非常抽象，内部是怎么实现的都没关系，只要能实现语义就行。
>
> 实际上很多解释器内部是以“编译器+虚拟机”的方式来实现的，先通过编译器将源码转换为AST或者字节码，然后由虚拟机去完成实际的执行。所谓“解释型语言”并不是不用编译，而只是不需要用户显式去使用编译器得到可执行代码而已。 
>
> 参考[13]

** 编译器 **


### 传统编译语言的编译过程

传统编译语言编写的源码在执行之前要经历的若干个步骤，统称为“编译”。下面的步骤参考《编译原理》<sup>[3]</sup>

** ① 分词/词法分析（Tokenizing/Lexing） **

编译器的第一个步骤称为词法分析（lexical analysis）或扫描（scanning）。

** ② 解析/语法分析 **

此步骤被称为语法分析（syntax analysis），或解析（parsing）都可以。语法分析的结果是给出前面的词法分析产生的词法单元流的语法结构。一个常用的表示就是语法树（syntax tree），树中的每个内部结点表示一个运算，而该结点的子结点表示该运算的分量。

** ③ 语义分析 **

语义分析的一个重要部分是类型检查（type checking）。

** ④ 中间代码生成 **

** ⑤ 代码优化 **

** ⑥ 代码生成 **

### JavaScript的编译过程（以V8引擎为例）

变量、函数提升

### 错误 & 异常

按照运行时期来分，JavaScript 有两类错误：语法分析期错误（SyntaxError）；运行时错误（语法解析没有问题，但是在运行时，查找变量或分配内存的过程中遇到了问题）。后者包括 ① 引用错误（ReferenceError），RHS 查询在所有嵌套的作用域中找不到所需的变量，JS 引擎就会抛出此错误；② 类型错误（TypeError），在通过 RHS 查询找到了一个变量，但是尝试对这个变量的值进行不合理的操作，则引擎就会抛出此类型的异常。

JavaScript 语言对于左值、右值的异常应该进行如何处理，也是 ECMAScript 标准进行严格的说明了的，例如：

> When an assignment occurs within strict mode code, its LeftHandSide must not evaluate to an unresolvable reference. If it does a ReferenceError exception is thrown upon assignment. The LeftHandSide also may not be a reference to a data property with the attribute value {[[Writable]]:false}, to an accessor property with the attribute value {[[Set]]:undefined}, nor to a non-existent property of an object whose [[Extensible]] internal property has the value false. In these cases a TypeError exception is thrown.
> 
> —— http://www.ecma-international.org/ecma-262/5.1/#sec-11.13

参考：[1]1.4节；[8]2.1.2节；[9]则给出了比较详细的示例代码。


## 词法作用域 vs 动态作用域

> 如果仅仅通过阅读某个语言的程序就可以确定其作用域，那么这个语言就使用了**静态作用域**，或者说词法作用域。否则这个语言就使用了**动态作用域**。
>
> ——《编译原理》第1章 page 23.

大部分编程语言的作用域规则是词法作用域，JavaScript 也不例外。词法作用域意味着作用域在词法解析阶段即已确定，不会改变。

> 词法作用域(lexical scope)等同于静态作用域(static scope)

参考资料 [5] 有对此较详细的描述：

> 大多数现在程序设计语言都是采用静态作用域规则，而只有为数不多的几种语言采用动态作用域规则，包括APL、Snobol和Lisp的早期方言……
> 
> ……静态作用域和动态作用域的一个重要区别在于：静态作用域规则查找一个变量声明时依赖的是源程序中块之间的静态关系；而动态作用域规则依赖的是程序执行时的函数调用顺序。说的具体点，就是静态作用域查找的是距离当前作用域最近的外层作用域中同名标识符的声明，而动态作用域则是查找最近的活动记录中的同名标识符声明。

## 函数作用域 vs 块作用域

### 函数形参默认值的作用域

ES6 允许为函数的形参指定默认值。如果参数默认值是个变量，则该变量在真正被赋值给形参时，是属于函数外部的作用域的。（参考 [2] 第73页）

```
var a = 'outer';

function foo(x = a) {
    var a = 'inner';
    console.log(x);
}

foo();  // 输出 outer
```

而下面的代码则会报错：

```
function foo(x = b) {
    var b = 'inner';  
    console.log(x);
}

foo();  // ReferenceError: b is not defined
```

## 关于 this

### this 的特性

首先可以参考MDN的资料<sup>[6]</sup>。

还可以参考《JavaScript忍者秘籍》<sup>[15]</sup>：

> 一个函数被调用时，除了传入了函数的显式参数外，名为 this 的隐式参数也被传入了函数。this 参数引用了与该函数调用隐式关联的一个对象，称为函数上下文（function context）。
>
> 函数上下文是一个来自于像 Java 这样的面向对象语言的概念。面向对象语言认为，this 是方法声明所在的类的一个实例。……事实证明，JavaScript 中的 this 参数与 Java 中的 this 不同，后者依赖于函数的声明，而 JavaScript 中的 this 则依赖于函数的调用方式。基于这个事实，将 this 称为**调用上下文（invocation context）**可能更为清晰。

`this` 表现出来足够的动态作用域特点，但是，JavaScript 依然是严格的词法作用域语言。为什么？因为 this 不是变量名，它是关键字。

另外值得注意的，就是箭头函数对于 this 的指向的词法锁定：无论一个箭头函数以怎样的方式被调用（对象方法，bind, call, apply），其 this 始终指向箭头函数声明所在作用域的 this。例如（代码参考[6]，最新版的 Chrome 浏览器是完美支持箭头函数的，可以在其中运行示例代码）：

```
var globalObject = this;
var foo = (() => this);
console.log(foo() === globalObject); // true

// Call as a method of an object
var obj = {foo: foo};
console.log(obj.foo() === globalObject); // true

// Attempt to set this using call
console.log(foo.call(obj) === globalObject); // true

// Attempt to set this using bind
foo = foo.bind(obj);
console.log(foo() === globalObject); // true
```

### `this` 关键字的由来

`this` 关键字似乎已经是面向对象语言的标配了。参考维基百科的解释 <sup>[7]</sup>:

> Object-oriented programming (OOP) is a programming paradigm based on the concept of "objects", which may contain data, in the form of fields, often known as attributes; and code, in the form of procedures, often known as methods. A feature of objects is that an object's procedures can access and often modify the data fields of the object with which they are associated (objects have a notion of "this" or "self")

JavaScript 的语法设计很大程度上参考了 Java 语言。具体来说，`this` 的设计，是对 Java 类中的 this 的模仿。不过，JavaScript 实际上并没有传统面向对象语言“类”的特性，其 `this` 跟 Java 相比也有很大的不同。

参考 [When should I use “this” in a class?](http://stackoverflow.com/questions/2411270/when-should-i-use-this-in-a-class)

C++ 也有 this 关键字，类的成员函数可以通过 this 访问到根据类创建的对象实例。

## 对象

函数是对象，但是函数对象是无法进行复制的。（存疑）

## 总结体会

** 其实很多细节问题，ECMAScript 标准都做出了解释说明。当对问题不清晰时，应该考虑一下直接读标准文档。 **

## 参考资料

1. 《你不知道的JavaScript（上卷）》,  [美] Kyle Simpson
2. 《ES6标准入门（第2版）》，阮一峰，2016
3. 《编译原理（第3版）》, Alfred V. Aho, etc.
4. 《操作系统》
5. [《静态作用域和动态作用域》](http://www.cnblogs.com/lienhua34/archive/2012/03/10/2388872.html)
6. [this | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
7. [Object-oriented programming | wikipedia](https://en.wikipedia.org/wiki/Object-oriented_programming)
8. 《JavaScript 语言精粹与编程实践（第2版）》，周爱民，2009
9. [浅谈JavaScript中的错误](http://www.html-js.com/article/On-the-error-in-JavaScript)
10. [JavaScript at 20, by Brendan Eich](http://brendaneich.github.io/ModernWeb.tw-2015/#1)
11. 《JavaScript 语言精粹（修订版）》, Douglas Crockford
12. [Java 是编译型语言还是解释型语言？ | 知乎](https://www.zhihu.com/question/19608553)
13. [虚拟机随谈（一）：解释器，树遍历解释器，基于栈与基于寄存器，大杂烩](http://rednaxelafx.iteye.com/blog/492667)
14. [V8 (JavaScript引擎) | 维基百科](https://zh.wikipedia.org/wiki/V8_(JavaScript%E5%BC%95%E6%93%8E))
15. 《JavaScript忍者秘籍》, Jhon Resig & Bear Bibeault 著, 徐涛 译, 2015年10月第1版.
