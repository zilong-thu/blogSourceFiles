---
title: Longest Valid Parenthese 问题的 DP 解法
date: 2019-03-20 23:39:09
tags: Algorithm
category: 算法
---

来源：[Leetcode: 32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)

> Longest Valid Parentheses
> Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.

要求子序列的所有括号是连续的、无间断的。

<!-- more -->

Example 1:

```
Input: "(()(()"
Output: 2
Explanation: The longest valid parentheses substring is "()"
```

Example 2:

```
Input: ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()"
```

我的思路，使用动态规划，可以找到其最优子结构。具体如下图所示：

<img src="/images/2019/03/longest-valid-parenthese-my-solution-q80.jpeg" />

JavaScript 的实现：

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var longestValidParenthesesDP = function(s) {
  let max = 0;
  let dp = new Array(s.length).fill(0);

  for (var i = 1; i < s.length; i++) {
    if (s[i] === '(') {
      dp[i] = 0;
    }

    if ((s[i] === ')') && (s[i - 1] === '(')) {
      let left = i - 2 >= 0 ? dp[i - 2] : 0;
      dp[i] = left + 2;
    }

    if ((s[i] === ')') && (s[i - 1] === ')')) {
      let x = i - dp[i - 1] - 1;
      if (x >= 0 && s[x] === '(') {
        let left = x - 1 >= 0 ? dp[x - 1] : 0;
        dp[i] = dp[i - 1] + left + 2;
      } else {
        dp[i] = 0;
      }
    }

    max = Math.max(max, dp[i]);
  }

  return max;
};

// 测试用例
let tests = [{
  s: "()"
}, {
  s: "(()",
}, {
  s: ")()())"
}, {
  s: "(()())"
}, {
  s: "(()()))))())"
}];

tests.forEach(item => {
  var res = longestValidParenthesesDP(item.s);
  console.log(res);
});
```
