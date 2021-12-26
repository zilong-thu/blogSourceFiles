---
title: 数组洗牌算法
date: 2015-02-03 23:01:23
tags: 算法
categories: JavaScript
sidebar: false
---
从<a href="http://book.douban.com/subject/25858070/" target="_blank">《JavaScript框架设计》</a>这本书里接触到洗牌算法：shuffle。

我就是个算法小白……痴。

<img src="/images/2015/01/QQimg201501.gif">

<a href="http://bost.ocks.org/mike/shuffle/" target="_blank">Fisher–Yates Shuffle, January 14, 2012 </a> 这篇文章用JavaScript数组讲解了洗牌算法，并且用D3.js演示各自的重排序效果。

<!-- more -->

###算法演进

下面的代码均来自该博文。

先来一发粗糙的算法：

```javascript 低效算法-01
function shuffle_1(array) {
  var copy = [], n = array.length, i;

  /* While there remain elements to shuffle… */
  while (n) {

    /* Pick a remaining element… */
    /* 问题就在于，越到最后，数组的空值就越多，就越难以选中 */
    i = Math.floor(Math.random() * array.length);

    /* If not already shuffled, move it to the new array. */
    if (i in array) {
      copy.push(array[i]);
      delete array[i];
      n--;
    }
  }

  return copy;
}
```

这个算法的问题就是，每次做随机运算时所用的范围，跟剩余的数组长度并不一致。导致最后很难“命中”，于是越来越慢，所需的循环次数往往要大于数组长度 n 。所以，随机过程需要有个可变的范围，其上限应该等于剩余的数组项的个数：

```javascript 改进的算法-02
function shuffle_2(array) {
  var copy = [], n = array.length, i;

  /* While there remain elements to shuffle… */
  while (n) {

    /* Pick a remaining element… */
    /* 优化之处：此时每次生成的随机数，都一定能够对应到一个剩余的数组项 */
    i = Math.floor(Math.random() * n--);

    /* And move it to the new array. */
    /* 新的性能瓶颈，splice方法…… */
    copy.push(array.splice(i, 1)[0]);
  }

  return copy;
}
```

上面的算法对随机过程进行了改进，可以保证在 n 次循环后就完成随机排序。循环次数下来了，下面该优化每一次循环时的操作了。此时的性能瓶颈在于，数组的`splice(i, 1)`操作，将下标为`i`的数组项删除掉，然后把后面的所有项依次向前移动一位。因此复杂度是O(n2)。

为什么一定要移动`(n-i)`个项呢？如果每次随机过程后，把挑出来的项跟队尾那个项交换一次，不就足够了么，毕竟下一次循环的时候`n`做了自减运算，数组的尾部就不再变动了。于是有：

```javascript 改进的算法-03
function shuffle_3(array) {
  var m = array.length, t, i;

  /* While there remain elements to shuffle… */
  while (m) {

    /* Pick a remaining element… */
    i = Math.floor(Math.random() * m--);

    /* And swap it with the current element. */
    t = array[m];  /* 把当前剩余数组的最后一项拿出来 */
    array[m] = array[i];  /* 跟选中的那个项换一下位置 */
    array[i] = t;  /* 这样就只是做了三次赋值运算而已，复杂度是1 */
  }

  return array;
}
```

这样一来，整个算法的复杂度就是O(n)啦。

要注意的是，原文中的算法-03是直接在传入的数组上进行操作的。也许先搞一个副本出来，对副本进行操作会更合适：

```javascript 改进的算法-04
function shuffle_4(arr) {
  /* 改变形参名，在局部作用域内创建arr的副本，要求arr必须严格是个数组 */
  var array = arr.slice(0);

  var m = array.length, t, i;

  /* While there remain elements to shuffle… */
  while (m) {

    /* Pick a remaining element… */
    i = Math.floor(Math.random() * m--);

    /* And swap it with the current element. */
    t = array[m];  /* 把当前剩余数组的最后一项拿出来 */
    array[m] = array[i];  /* 跟选中的那个项换一下位置 */
    array[i] = t;  /* 这样就只是做了三次赋值运算而已，复杂度是1 */
  }

  return array;
}
```

###性能对比
测试用一个较长的数组，`n=10000`。

```javascript
var arr = [];
for(var i=0; i<10000; i++){ arr.push(i); }

console.time('shuffle-01');
shuffle_1(arr);
console.timeEnd('shuffle-01');

console.time('shuffle-02');
shuffle_2(arr);
console.timeEnd('shuffle-02');

console.time('shuffle-04');
shuffle_4(arr);
console.timeEnd('shuffle-04');

```

在 Chrome 39 中的结果是这样的：

+ shuffle-01: 28.587ms
+ shuffle-02: 14.757ms
+ shuffle-04: 0.120ms