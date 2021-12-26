---
title: About ECMAScript Async Functions
date: 2018-01-21 19:14:21
tags: JavaScript
categories: JavaScript
---

Async Functions are introduced officially in ECMAScript 7 Spec, as the ultimate way to solve async code writting problems (especially the famous *callback hell*).

We can easily get the resolved value of a Promise with `await` operator.

## Syntax

```
[rv] = await expression;
```

### expression

A `Promise` or any value to wait for.

### rv

Returns the fulfilled value of the promise, or the value itself if it's not a `Promise`.

See also: [await | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)

<!-- more -->

## Serial Async Execution

```javascript
let totalTime = 0;

function timeout(order) {
  let time = Math.floor(200 * Math.random());

  totalTime += time;

  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({
        order,
        time,
      });
    }, time);
  });
}

async function asyncPrint() {
  console.log('start');
  console.time('asyncPrint');
  for (var i = 0; i < 5; i++) {
    let a = await timeout(i);
    console.log(`[${i}] => `, a);
  }
  console.log('end');
  console.timeEnd('asyncPrint');
  console.log('totalTime => ', totalTime, 'ms');
}

asyncPrint();
```

The code above will print objects in the original loop order, each `timeout` function was executed in serial sequence, resulting the total ellapsed time to be the sum of all loops:

```shell
start
[0] =>  { order: 0, time: 38 }
[1] =>  { order: 1, time: 176 }
[2] =>  { order: 2, time: 163 }
[3] =>  { order: 3, time: 73 }
[4] =>  { order: 4, time: 71 }
end
asyncPrint: 538.578ms
totalTime =>  521 ms
```

## Parall Async Procedure

no matter how much time each one take

```javascript
async function asyncPrintParall() {
  console.log('start');
  console.time('asyncPrintParall');
  let arr = [];
  for (var i = 0; i < 5; i++) {
    arr.push(timeout(i));
  }

  let arrRes = await Promise.all(arr);
  for (let res of arrRes) {
    console.log(res);
  }
  console.log('end');
  console.timeEnd('asyncPrintParall');
}

asyncPrintParall();
```


```
start
{ order: 0, time: 19 }
{ order: 1, time: 127 }
{ order: 2, time: 155 }
{ order: 3, time: 177 }
{ order: 4, time: 111 }
end
asyncPrint: 182.966ms
```
