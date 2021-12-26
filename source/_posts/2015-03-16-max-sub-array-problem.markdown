---
title: 最大子数组问题
date: 2015-03-16 19:47:08
tags: 算法
categories: 算法
sidebar: false
---
最大子数组问题仅对存在负数的数组才有意义。

这里根据《算法导论（第三版）》第四章的4.1节的练习题 4.1-5进行拓展研究。用JavaScript实现最大子数组的查找。

<!-- more -->

## 准备工作

先定义两个用于生成随机数组的函数：

```
/* 生成一个m、n之间的整数 */
function random(m,n){ 
    var i=Math.random(); 
    return Math.round((n-m)*i+m); 
}

/* min: 数组下界
 * max: 数组上界
 * len：生成的数组的长度
 */
function getRandomArr(min,max,len){ 
    var resultArr=[]; 
    for(var i=0;i<len;i++){ 
        resultArr.push(random(min,max));
    } 
    return resultArr; 
}

var A = getRandomArr(-20, 20, 6);
console.log(A);
```

## 线性复杂度的算法：Kadane算法

这个问题用动态规划的方法来解决会更有效，而不是分治策略。例如下面的Kadane算法。

算法的关键在于：遍历、累加的过程中，曾经出现的最大子数组是会被记录下来的。重置下标时，只是为了寻找“潜在”的下一个最大子数组。

```
/* Kadane算法
 * 又被称为扫描法
 * 该算法用到了一个启发式规则：如果前面一段连续子数组的和小于0，那么就丢弃它。
 */
function Kadane(A){
    var max = A[0],
    sum = 0,
    sum_low = 0, sum_high = 0, /* 这两个值保存了当前累计计算的、和非负的子数组的下标起始值 */
    max_low = 0, max_high = 0;

    for(var i = 0; i<A.length; i++){
        if(sum >= 0){
            sum += A[i];
            sum_high = i;
        } else{
            sum = A[i];
            sum_low = i;
            sum_high = i;
        }

        if(sum > max){
            max_low = sum_low;
            max_high = sum_high;
            max = sum;
        }
    }

    return {
        maxSum: max,
        low: max_low,
        high: max_high
    };
}

console.log(Kadane(A));
```