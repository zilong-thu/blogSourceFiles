---
title: about quick sort algorithm
date: 2017-09-19 00:49:49
tags:
categories: 算法
---

## 历史

快速排序算法是由 Tony Hoare 在 1959 年发明的。如果实现得好，那么它的速度可以达到其两个主要竞争者——归并排序与堆排序——的两倍到三倍以上。

在高速的实现中，快排是一个不稳定的算法，即它不能保证原来相等的值的位置不变。

<!-- more -->

## 思路

`pivot`，为“中枢、支点”的意思。

``` javascript
// 选取中间值为基准点、高效的实现示例
function quickSort(A) {
  if (A.length <= 1) {
    return A;
  }

  var pivot = Math.floor(A.length / 2);
  var left = [];
  var right = [];

  for (var i = 0; i < A.length; i++) {
    if (i === pivot) {
      continue;
    } else if (A[i] <= A[pivot]) {
      left.push(A[i]);
    } else {
      right.push(A[i]);
    }
  }

  return quickSort(left).concat([A[pivot]].concat(quickSort(right)));
}


var A = [1, 3, 89, 5, 34, 2, 80, 50];
var b = quickSort(A);
console.log(b);
```

## 参考

第一篇是维基百科的词条，非常有意思。

+ https://en.wikipedia.org/wiki/Quicksort
+ http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html
