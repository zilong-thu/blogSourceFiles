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

JavaScript 里的 Number 是完全按照 IEEE Standard for Floating-Point Arithmetic (IEEE 754)<sup>[1,2]</sup> 实现的。Number 类型实际上就是 64 位的浮点数类型。

任意数值都可以用科学计数法来表示，例如：

<img src="/images/2018/06/scientific-notation.png" style="max-width: 400px;">


## 参考资料

1. [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)
2. [Double precision floating-point format(pdf)](/files/2018/06/Double-precision-floating-point-format-Wikipedia.pdf)
3. [BigInts in JavaScript: A case study in TC39](https://docs.google.com/presentation/d/1apPbAiv_-mJF35P31IjaII8UA6TwSynCA_zhfDEmgOE/edit#slide=id.p)，[PDF](/files/2018/06/BigInts_in_JavaScript_A_case_study_in_TC39.pdf)
4. [双精度浮点运算指令.pdf](/files/2018/06/双精度浮点运算指令.pdf)
