---
title: 最近遇到的一些算法题
tags: 算法
---


## Sum 问题及其变体的解答与分析（✓）

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


## 任务控制器（✓）

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

## 顺序控制（尾调用）（✓）

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


## 找到二叉树中两个节点的最近公共父节点

```javascript
class TreeNode () {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}

// 关键在于遍历这个二叉树，按照深度优先遍历，分别记录两个节点的访问路径
```

## 最大岛屿问题，Leetcode 695 题

```javascript
const grid = [
  [0,0,1,0,0,0,0,1,0,0,0,0,0],
  [0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,1,1,0,1,0,0,0,0,0,0,0,0],
  [0,1,0,0,1,1,0,0,1,0,1,0,0],
  [0,1,0,0,1,1,0,0,1,1,1,0,0],
  [0,0,0,0,0,0,0,0,0,0,1,0,0],
  [0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,0,0,0,0,0,0,1,1,0,0,0,0]
];

function maxAreaOfIsland(grid) {
  const m = grid.length;
  const n = grid[0].length;
  let max = 0;
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (grid[i][j]) {
        max = Math.max(max, areaOfIland(grid, i, j));
      }
    }
  }
}

function areaOfIland(grid, i, j) {
  if ()
}
```

## 猿辅导-已有 Promise，请实现 Promise.all（✓）

```javascript
readFile((file, (err, data) => {
  if (err) {
    return err;
  }

  return data;
}));

// 先进行 Promisify
function readFileP(file) {
  return new Promise((resolve, reject) => {
    readFile((file, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    }));
  });
}

Promise.all = function(list) {
  return new Promise((resolve, reject) => {
    const len = list.length;
    const resList = new Array(len).fill(undefined);
    for (let i = 0; i < len; i++) {
      readFileP(list[i]).then(data => {
        list[i] = data;
        // 检查是否 list 里的每一项都不是 undefined 了
        if (list.every(item => item !== undefined)) {
          resolve(list);
        }
      }).catch(err => {
        reject(err);
      })
    }
  });
}
```

## 二叉树层序遍历

```javascript
class TreeNode() {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}

const tree = {
  val: 1,
  left: {
    val: 2,
    left: {
      val: 4,
      left: {
        val: 8,
      },
      right: {
        val: 9
      }
    },
    right: {
      val: 5,
      left: {
        val: 10,
      },
      right: {
        val: 11
      }
    }
  },
  right: {
    val: 3,
    left: {
      val: 6,
      left: {
        val: 12
      }
    },
    right: {
      val: 7
    }
  }
}

/**
 * 这个是深度优先遍历，是比较好实现的
 */
function walkTree(root) {
  const res = [];

  const visit = (node) => {
    if (node) {
      res.push(node.val);
      visit(node.left);
      visit(node.right);
    }
  }

  visit(root);

  console.log(res);
}
```
