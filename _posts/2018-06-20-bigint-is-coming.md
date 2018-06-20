---
title: BigInt is Coming
date: 2018-06-20 21:20:34
tags: JavaScript
category: JavaScript
---

BigInt is Coming!

<!-- more -->

## Number 的问题

<img src="/images/2018/06/js-number.jpg" style="max-width: 400px;">

JavaScript 里的 `Number` 是完全按照 IEEE Standard for Floating-Point Arithmetic (IEEE 754)<sup>[1,2]</sup> 实现的。`Number` 类型实际上就是 64 位浮点数（Double precision floating-point format），也就是俗称的 `Double` 类型。

任意数值都可以用科学计数法来表示，例如：

<img src="/images/2018/06/scientific-notation.png" style="max-width: 400px;">

Double 类型数值的存储结构如下图所示，使用了64位二进制，最左边一位是符号位，之后11位是指数位，后面的52位是浮点数部分：

<img src="/images/2018/06/ieee-double-64.jpeg" style="max-width: 600px;">

由于需要有很多位用来表示浮点数，因此 Double 类型所能够表示的整数范围远远小于 2<sup>64</sup>。

```javascript
console.log(Number.MAX_SAFE_INTEGER);
// => 9007199254740991

Number.MAX_VALUE;
// => 1.7976931348623157e+308
```

尤其在到边界后，Double 类型就无法表示更大的整数了。

社区给出了若干方案，bn.js。

## 新的类型：BigInt

## 参考资料

1. [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)
2. [Double precision floating-point format(pdf)](/files/2018/06/Double-precision-floating-point-format-Wikipedia.pdf)
3. [BigInts in JavaScript: A case study in TC39](https://docs.google.com/presentation/d/1apPbAiv_-mJF35P31IjaII8UA6TwSynCA_zhfDEmgOE/edit#slide=id.p)
4. [双精度浮点运算指令.pdf](/files/2018/06/双精度浮点运算指令.pdf)
