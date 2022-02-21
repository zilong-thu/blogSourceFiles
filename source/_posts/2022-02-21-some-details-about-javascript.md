---
title: JavaScript 的一些细节
date: 2022-02-21 13:09:04
tags: JavaScript
---

最近看了篇文章，[「2021」高频前端面试题汇总之JavaScript篇（上），作者：CUGGZ](https://juejin.cn/post/6940945178899251230)，感觉文章不错，知识点很全。但是有些细节我研究了一下，对自己有些许帮助，特此记录下来。

<!-- more -->

## typeof null 的结果是什么，为什么？

> 在 JavaScript 第一个版本中，所有值都存储在 32 位的单元中，每个单元包含一个小的 类型标签(1-3 bits) 以及当前要存储值的真实数据。类型标签存储在每个单元的低位中，共有五种数据类型：
> 000: object   - 当前存储的数据指向一个对象。
> 1: int      - 当前存储的数据是一个 31 位的有符号整数。
> 010: double   - 当前存储的数据指向一个双精度的浮点数。
> 100: string   - 当前存储的数据指向一个字符串。
> 110: boolean  - 当前存储的数据是布尔值。
> 如果最低位是 1，则类型标签标志位的长度只有一位；如果最低位是 0，则类型标签标志位的长度占三位，为存储其他四种数据类型提供了额外两个 bit 的长度。
> 那也就是说null的类型标签也是000，和Object的类型标签一样，所以会被判定为Object。

其实上面说了程序方面的实现，而 Breidan 本人则没想那么多，就是把 null 按照 Object 来实现的。

## `undefined` 不是保留字

`undefined` 在 JS 语言里不是保留字，意味着 undefined “可以”当做标识符使用，可以被赋值，例如：

```javascript
var undefined = 0;
```

但是，上面的语句会自动失效，即 `undefined` 的值依然是 `undefined`，与 `（void 0）` 的返回值一样。

## 关于 `ToPrimitive` 方法

原生 JS 里不存在这个全局方法，而是内置（即不对外暴露）在每个 JS 值上的：`Symbol.ToPrimitive`。定义：

> The Symbol.toPrimitive is a symbol that specifies a function valued property that is called to convert an object to a corresponding primitive value.

举个例子（MDN 官方例子）：

```javascript
const object1 = {
  [Symbol.toPrimitive](hint) {
    if (hint === 'number') {
      return 42;
    }
    return null;
  }
};

console.log(Number(object1));  // 输出 42
```

## 数组的 rest 操作符

`Math.min()` 与 `Math.max()` 都是可以接受任意个数参数的，因此如果是数组，可以借助 rest 操作符展开数组：

```javascript
let arr = [1, 3, 8, 2, 4, 9];
console.log(Math.min(...arr));  // 1
console.log(Math.max(...arr));  // 9
```

## 关于 Proxy

Proxy 对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。

基本语法：

```javascript
const target = {};
const handler = {
  set(target, key, value) {
    console.log('key: ', key, ' => ', value);
    target[key] = value;
  }
};
const p = new Proxy(target, handler);
```

+ `target`: 被 Proxy 代理虚拟化的对象，可以是任何类型的对象，包括原生数组，函数，甚至另一个代理。它常被作为代理的存储后端。——这里的“存储后端”，意味着我们实际使用时，一般通过 `p` 来访问、操作对象，而非使用 `target`。
+ `handler`: 一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 p 的行为。

不过，`p instanceof Proxy` 却会报错，`p instanceof Object` 则正常返回 `true`。

`Proxy` 的兼容性肯定是越来越好，PC 端、主流的移动设备，基本问题不大了。

## 如何提取深度嵌套的对象里的指定属性？

这个比较有意思。解构赋值在处理层级较浅的对象时很清晰，但是如果对象层级比较深，其实也是有办法解构的：

```javascript
const school = {
   classes: {
      stu: {
         name: 'Bob',
         age: 24,
      }
   }
};

const { classes: { stu: { name } }} = school;
console.log(name);  // Bob
```

注意此时其他的层级（例如 classes、stu）都不是解构赋值，即解构赋值仅作用于最末层。

```javascript
console.log(stu);  // Uncaught ReferenceError: stu is not defined
```
