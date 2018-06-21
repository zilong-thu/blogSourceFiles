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

尤其在到边界后，Double 类型表示的整数会出现不准的情况。例如：

```javascript
14411518807585585000 + 1;  // 依然返回 14411518807585585000，意味着 1 是加不上去的
```

## 新的类型：BigInt

### 当前状态

ECMAScript 为此提出的解决方案是引入新的基本类型：`BigInt`。在今年的 JSConf EU（欧洲 JavaScript 开发者大会）上，TC39 的成员做了一个对 `BigInt` 的介绍<sup>[4]</sup>。

这个基本类型目前处在 Stage3 阶段。此阶段的含义是 Candidate（候选），“Indicate that further refinement will require feedback from implementations and users”，即需要实现者（通常是浏览器厂商）与用户进一步优化与反馈。

<img src="/images/2018/06/tc39-bigint-title.png" style="max-width: 500px;">

目前 Chrome67 已经开始支持 BigInt。还需要有至少一个其他浏览器厂商或JS运行时给出具体的实现。

### 特性

ECMAScript 的 `BigInt` 类型的值，字面量是直接在数字后面加小写字母 `n`：

```javascript
var a = 800n;
var b = 9007199254740991n;

a * b;  // 返回 7205759403792792800n
```

这是继 `string`, `number`, `boolean`, `null`, `undefined`, `symbol` 这 6 个基本类型之后的又一个基本类型。意味着 `typeof` 运算符可以准确地判断相应值的类型。

```javascript
typeof 2n;  // 输出 'bigint'
```

在进行数值计算时，`Number` 与 `BigInt` 之间没有隐式转换；但是布尔运算则可能会有隐式转换。

数值计算：

```javascript
2 * 2n;
// 会直接报错：Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions

BigInt(2) * 2n;  // 返回 4n
```

布尔运算：

```javascript
2 == 2n;   // 返回 true
2 === 2n;  // 返回 false，因为二者类型不相同
```

## 参考资料

1. [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)
2. [Double precision floating-point format(pdf)](/files/2018/06/Double-precision-floating-point-format-Wikipedia.pdf)
3. [双精度浮点运算指令.pdf](/files/2018/06/双精度浮点运算指令.pdf)
4. [BigInts in JavaScript: A case study in TC39](https://docs.google.com/presentation/d/1apPbAiv_-mJF35P31IjaII8UA6TwSynCA_zhfDEmgOE/edit#slide=id.p)
5. [Proposal: BigInt | tc39](https://tc39.github.io/proposal-bigint/)
