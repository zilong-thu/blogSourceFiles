---
title: 关于归并排序
date: 2017-09-19 00:22:33
tags:
categories: 算法
---

## 概述

归并排序（Merge-Sort），由冯诺依曼于 1945 年发明，是分治法（Divide and Conquer）的经典案例。


## JavaScript 实现

代码思路比较清晰：

+ 首先，需要一个执行合并有序数组的函数（将两个有序表合并成一个有序表，称为二路归并）。
+ 然后，对需要排序的数组进行拆分，分别对左右两个子数组进行归并排序，然后合并。这样就形成了递归

<!-- more -->

``` javascript
var A = [1,  3, 5, 7, 9, 10, 87];
var B = [2, 27, 100, 109];

/**
 * 二路归并
 * @param  {Array} nums1 [数组]
 * @param  {Array} nums2 [数组]
 * @return {Array}       [数组]
 */
function merge(nums1, nums2) {
  var m = nums1.length;
  var n = nums2.length;

  var C = [];
  var i = 0;
  var j = 0;
  var k = 0;

  while (i < m && j < n) {
    if (nums1[i] < nums2[j]) {
      C[k++] = nums1[i++];
    } else {
      C[k++] = nums2[j++]
    }
  }

  while (i < m) {C[k++] = nums1[i++];}

  while (j < n) {C[k++] = nums2[j++];}

  return C;
}

var C = merge(A, B);
console.log(C);


/**
 * 归并排序函数
 * @param  {Array} arr [数组]
 * @return {Array}     [数组]
 */
function mergeSort(arr) {
  var len = arr.length;

  if (len === 1) return arr;

  var mid = Math.floor(len / 2);
  var leftArr = arr.slice(0, mid);
  var rightArr = arr.slice(mid);

  return merge(mergeSort(leftArr), mergeSort(rightArr));
}

var D = [1, 3, 5, 2, 27, 7, 100, 9, 109, 10, 87];
var D_sorted = mergeSort(D);
console.log('D_sorted: ', D_sorted);
```

## 算法复杂度估算

每次的合并，复杂度都是 O(N)。由于是按照二分进行拆分的，那么像二叉树一样，共有 logN 层。于是本算法的时间复杂度为 O(NlogN)。
