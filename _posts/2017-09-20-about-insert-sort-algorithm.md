---
title: 关于插入排序
date: 2017-09-20 22:14:49
tags:
categories: 算法
---

## 思路

<img src="/images/2017/09/insert-sort.jpg">

<!-- more -->


## 代码（JavaScript 实现）

```
var A = [1, 3, 5, 2, 27, 7, 100, 9, 109, 10, 87, 800, 988, 7665, 83790, 28, 34];
var B = [10, 9, 8, 7, 6, 5, 4, 3, 2, 1];
/**
 * 插入排序
 * @param  {Array} A [数组]
 * 不返回值，原地进行排序，这样可以有较低的空间复杂度
 */
function insertSort(A) {
  var O = 0;
  if (A[0] > A[1]) {
    var tmp = A[1];
    A[1] = A[0];
    A[0] = tmp;
  }

  for (var i = 2; i <= A.length-1; i++) {
    var end = A[i];
    // 寻找插入位置，根据要升序还是要降序来决定从后向前还是从前向后查找
    if (end > A[i-1]) {
      continue;
    }
    for (var j = i-1; j >= 0; j--) {
      if (((end >= A[j]) && (end <= A[j+1])) || (end < A[0] && (j === 0))) {
        // 开始移动
        for (var k = i; k > j; k--) {
          A[k] = A[k-1];
          O++;
        }

        if ((end < A[0]) && (j === 0)) {
          A[0] = end;
        } else {
          A[j+1] = end;
        }
      }
    }
  }

  console.log(A);
  console.log(O);
}

insertSort(A);
insertSort(B);
```

## 算法复杂度

最好的情况，数组已经是升序了，那么时间复杂度是 O(N)。最坏的情况，i 循环 N，内部每个 i 循环 N-i，次，移动 N-i 个元素，复杂度可以达到 O(N<sup>3</sup>)，感觉不靠谱……
