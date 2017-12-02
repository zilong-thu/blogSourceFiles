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

## 思路1：非原址排序

`pivot`，为“中枢、支点”的意思。

```javascript
/**
 * q-1.js
 */
// 选取中间值为基准点、高效的实现示例
function quickSort1(A) {
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
var b = quickSort1(A);
console.log(b);
```

## 思路2：原址排序

上面的排序实现里，`left`、`right` 数组都是新创建的，无疑会增加内存的消耗。这里再给出一版基于原址的快速排序实现。

```javascript
/**
 * q-2.js
 */
function quickSort2(A) {
  /**
   * 在原数组中交换数组里的两个项
   */
  var swapArrayItems = function(arr, i, j) {
    var tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
  };

  /**
   * 在原数组中寻找分隔左右数组的项的下标
   */
  var partition = function(arr, left, right) {
    var pivot = arr[Math.floor((left + right) / 2)];
    var i = left;
    var j = right;
    while(i < j) {
      while(arr[i] < pivot) i++;
      while(arr[j] > pivot) j--;
      if (i <= j) {
        swapArrayItems(arr, i, j);
        i++;
        j--;
      }
    }
    return i;
  };

  /**
   * 原址快排函数
   */
  var quick = function(arr, left, right) {
    var index;
    if (arr.length > 1) {
      index = partition(arr, left, right);

      if (left < index - 1) {
        quick(arr, left, index - 1);
      }

      if (right > index) {
        quick(arr, index, right);
      }
    }
  };

  return quick(A, 0, A.length - 1);
}
```

## 原址与非原址快排的性能对比

如果对上面的两种实现以及 JavaScript 内置的 sort 方法进行性能对比，会发现原址排序快很多。

```javascript
/**
 * test.js
 */
var q1 = require('./q-1.js');
var q2 = require('./q-2.js');

var arr1 = [3, 5, 1, 6, 4, 7, 2];
var arr2 = arr1.map(i => i);
var arr3 = arr1.map(i => i);
var NUM = 100000;

console.time('非原址排序');
for (var i = 0; i < NUM; i++) {
  q1(arr1);
}
console.timeEnd('非原址排序');

console.time('原址排序');
for (var i = 0; i < NUM; i++) {
  q2(arr2);
}
console.timeEnd('原址排序');

console.time('Array.prototype.sort');
for (var i = 0; i < NUM; i++) {
  arr3.sort();
}
console.timeEnd('Array.prototype.sort');

console.log('arr1 => ', arr1);
console.log('arr2 => ', arr2);
console.log('arr3 => ', arr3);
```

运行时间对比（10万次）：

+ 非原址排序: 246.511ms
+ 原址排序: 31.057ms
+ `Array.prototype.sort`: 56.593ms

注：数组原型上的 `sort` 方法有不同的实现，根据参考资料[3]，Mozilla Firefox 使用了归并排序来实现 sort，而 Chrome 则使用了快排的一种变体。

## 参考

第一篇是维基百科的词条，非常有意思。

1. https://en.wikipedia.org/wiki/Quicksort
2. http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html
3. Loiane Groner. 学习JavaScript数据结构与算法[M], 北京: 人民邮电出版社, 2015.
