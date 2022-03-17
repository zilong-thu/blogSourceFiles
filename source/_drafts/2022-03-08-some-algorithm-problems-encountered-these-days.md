---
title: 最近遇到的一些算法题
tags: 算法
---


## Sum 问题及其变体的解答与分析

```javascript
function sum(...args) {
  let val = args.reduce((a, b) => a + b, 0);

  let fn = function(...args2) {
    val = args2.reduce((a, b) => a + b, val);
    return fn;
  }

  fn.valueOf = () => val;

  fn.sumOf = () => val;

  return fn;
}

var a = sum(1, 2, 3).sumOf();
console.log(a);

var b = sum(1, 2)(4, 5).sumOf();
console.log(b);

var c = sum(1)(2)(3)(4).sumOf();
console.log(c);
```


## 任务控制器

```javascript
/**
 * 一个带并发限制的异步调度器schedule，保证同时运行的任务最多有两个
 * list Array<() => Promise<void>> 是一个每一项都是返回 Promise 的函数组成的数组
 */
function taskRunner(list, parallSize) {
  console.log('list 剩余任务数: ', list.length);
  let arr = list.slice(0, parallSize);

  Promise.all(arr.map(fn => fn()))
    .then(() => {
      list.splice(0, parallSize);

      if (list.length) {
        taskRunner(list, parallSize);
      } else {
        console.log('执行完成');
      }
    })
}

const list = [];
function fn() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('heihei');
      resolve()
    }, 300);
  });
}

for (let i = 0; i < 11; i++) {
  list.push(fn);
}

taskRunner(list, 2);
```

## 给出一个字符序列的全排列结果

## LRU 置换算法相关

## 找出小于 n 的所有质数的个数

```javascript
/**
 * @param {number} n
 * @return {number}
 * Sieve of Eratosthenes 算法
 */

/**
 * @param {number} n
 * @return {number}
 */
var countPrimes = function(n) {
  /**
   * true: 是合数
   * false: 是质数
   * 默认填充为 false
   */
  let nonPrimeList = new Array(n).fill(false);
  let res = 0;
  nonPrimeList[1] = false;
  nonPrimeList[2] = false;
  for (let num = 2; num < n; num++) {
    if (nonPrimeList[num]) {
      continue
    }

    res++
    for (let i = 2; i * num < n; i++) {
      nonPrimeList[i * num] = true
    }
  }
  return res;
};
```

## 质因数分解

问题描述：给一个整数 N，找出所有乘积等于 N 的质数组合。

```javascript
/**
 * 判断 n 是否为质数
 */
function prime(n) {
  const primeList = new Array(n).fill(true);
}
```

翻了一下《算法导论》，挺复杂的。

## 顺序控制（尾调用）

问题：已知 getData 是一个可以返回 Promise 的函数，要求不使用 async/await，实现一个 getDataList，可以确保每个 getData 是串行调用的。

```javascript
function getData(url: string): Promise<void> {
  return Promise.resolve('');
}

function getDataList(urls: string[]): Promise<void> {
  const result = [];
  const len = urls.length;

  if (!len) {
    return Promise.resolve([]);
  }

  return new Promise((resolve, reject) => {
    const next = (index) => {
      const url = urls[index];
      getData(url)
        .then((res) => {
          result.push(res);
          if (index < urls.length - 1) {
            next(index + 1);
          } else {
            resolve(results);
          }
        })
        .catch(err => {
          reject(err);
        });
    };

    next(0);
  });
}
```
