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
