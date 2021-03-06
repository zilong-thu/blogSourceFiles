---
title: Koa Running On HTTP/2
date: 2017-03-20 13:52:00
tags: 
  - Node.js
  - Koa
  - HTTP
categories: HTTP
---

## HTTP2 概述

翻译自 [Hypertext Transfer Protocol Version 2 (HTTP/2)](https://tools.ietf.org/html/rfc7540) 的简介：

> HTTP/2 enables a more efficient use of network resources and a reduced perception of latency by introducing header field compression and allowing multiple concurrent exchanges on the same connection.  It also introduces unsolicited push of representations from servers to clients.
> 
> HTTP/2 可以提升对网络资源的使用效率，并且通过压缩头部、在同一个连接里多路复用，来减少网络等待时间。HTTP/2 还引入了服务器推送的技术。

<!-- more -->

## SPDY

SPDY 是 HTTP/2 的前身，是目前使用应用最广泛的 HTTP/2 实现。

> SPDY（读作“SPeeDY”）是Google开发的基于TCP的传输层协议，用以最小化网络延迟，提升网络速度，优化用户的网络使用体验。SPDY并不是一种用于替代HTTP的协议，而是对HTTP协议的增强。新协议的功能包括数据流的多路复用、请求优先级以及HTTP报头压缩。
> 
> —— [SPDY | 百度百科](http://baike.baidu.com/item/SPDY?sefr=enterbtn)

## Koa 2 + SPDY

这里的例子直接使用最新版本的 Koa 2，其要求使用 Node.js 至少 7.6 版本，我用的是 7.8。

```
# using Node.js@7.8.0

# 使用的几个主要的包：
  "http2": "^3.3.6",
  "koa": "^2.2.0",
  "spdy": "^3.4.4"
```

改造的时候，思路也比较简单。koa 模块输出的是一个类，Koa，其有个原型方法：

```
  # http.createServer 的参数是个函数 requestListener
  # requestListener的参数分别为 request、response
  listen() {
    debug('listen');
    const server = http.createServer(this.callback());
    return server.listen.apply(server, arguments);
  }

  # 其中，this.callback() 做的事情，是把所有的中间件，合并为一个中间件
```

那么，基于 HTTPS、SPDY、HTTP/2 的 koa app 只需要继承 Koa，重写 listen 方法即可。例如基于 SPDY 的 Koa 应用：

```
const Koa = require('koa');
const spdy  = require('spdy');
const options = require('./keys');  // 这里输出的是证书


class KoaOnHttps extends Koa {
  constructor() {
    super();
  }

  listen() {
    const server = spdy.createServer(options, this.callback());
    return server.listen.apply(server, arguments);
  }
}

const app = new KoaOnHttps();

// x-response-time
app.use(async function (ctx, next) {
  const start = new Date();

  await next();

  const ms = new Date() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
});

// logger
app.use(async function (ctx, next) {
  const start = new Date();

  await next();

  const ms = new Date() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}`);
});

app.listen(3000);
```

## 如何生成证书

目前，Node.js 生态里无论是 SPDY，还是 HTTP2，都是基于 HTTPS 的。那么开发中需要自己配置签名和证书。参考一篇博客：[https://juejin.im/entry/57f3a546da2f60004f6eebbc](https://juejin.im/entry/57f3a546da2f60004f6eebbc)。

```
$ mkdir keys
$ cd keys
$ openssl genrsa -des3 -passout pass:x -out server.pass.key 2048 
$ openssl rsa -passin pass:x -in server.pass.key -out server.key
$ openssl req -new -key server.key -out server.csr
# ...
# 输入证书相关信息（随意填写）
# ...
$ openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt
$ rm server.pass.key
```

## 实现一个简单的静态文件服务器

+ 资源类型
+ 处理404

## 代码地址


## 当前使用了 HTTP/2 技术的公司

+ [淘宝首页](https://www.taobao.com/)
+ [又拍云](https://www.upyun.com/)


