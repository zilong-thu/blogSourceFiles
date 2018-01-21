---
title: About ECMAScript Async Function
date: 2018-01-21 19:14:21
tags: JavaScript
categories: JavaScript
---

Async Function is introduced officially in ECMAScript 7 Spec, as the ultimate way to solve async code writting problems.

We can easily get the resolved value of a Promise with `await`.

<!-- more -->

```javascript
function timeout() {
  let time = Math.floor(100 * Math.random());
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({
        status: 200,
        body: {time},
        request: {}
      });
    }, time);
  });
}

async function asyncPrint() {
  for (var i = 0; i < 10; i++) {
    let a = await timeout();
    console.log(`[${i}] => `, a);
  }
}

asyncPrint();
```

The code above will print objects in the original loop order, no matter how much time each one take:

<img src="/images/2018/01/async-func-res.png" style="max-width: 500px" />

