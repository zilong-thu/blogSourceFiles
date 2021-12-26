---
title: 关于在HTTP请求BODY中使用JSON格式
date: 2015-10-27 23:15:06
tags: HTTP

---

在使用 HTTP POST 或者 PUT 方法向服务器发送非`multipart`类型的数据时，有两种最常用的对消息主体进行编码的方法，它们分别对应不同的`Content-Type`：

| `Content-Type`| 类型说明 | 示例
| ----- | ----- | ------ | ------ |
| `application/x-www-form-urlencoded` | 以 URLEncoded 的方式进行编码 | `name=test&sub%5B%5D=1&sub%5B%5D=2` 解码后就是：`name=test&sub[]=1&sub[]=2` |
| `application/json` | 消息主体是序列化后的 JSON 字符串，要求服务器端能够支持JSON | `{"name":"test","sub":[1,2]}` |

<!-- more -->

HTTP 为超文本传输协议，整个的 HTTP 报文，如果按编程语言里面的类型来分的话，就是一大段字符串。值得注意的是，不像 JSON，`application/x-www-form-urlencoded` 的方式对复杂类型（例如数组）的处理，并没有严格的标准。有的接口使用 `key[]=a&key[]=b` 来表示数组 `key: ['a', 'b']`，（这也是最常见的，jQuery、superagent等客户端会如此编码），有的库则将数组编码为：`key=a&key=b`，有的则是携带下标进行编码：`key[0]=a&key[1]=b`……十分混乱。所以如果是数组且数组的每一项为简单基本类型，而且非要用 `application/x-www-form-urlencoded` 进行序列化，那么不如用英文逗号分隔的字符串来表示。如果是嵌套对象……那么还是尽早使用 JSON 吧。

## 浏览器端
HTML表单默认是按照`application/x-www-form-urlencoded`进行编码的。

### XMLHttpRequest
如果使用浏览器原生的HTTP请求方法`XMLHttpRequest`，那么只需在open一个请求之后、send该请求之前，设置一下请求首部的`Content-Type`字段即可。示例代码如下：

```
var xhr = new XMLHttpRequest();
xhr.open('POST', 'http://url', true);
xhr.setRequestHeader('Content-Type', 'application/json');

/* 所有相关的事件绑定必须在调用send()方法之前进行 */
xhr.onreadystatechange = function(){
    var state = xhr.readyState;
    if(state == 4 && xhr.status == 200){
        /* 响应成功, do something */
    }
};

xhr.send(JSON.stringify(object));
```

参考：[XMLHttpRequest | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

### jQuery
如果使用jQuery，默认情况下，其`contentType`值为：`application/x-www-form-urlencoded; charset=UTF-8`。可以通过将其设为`application/json`，然后将数据进行JSON序列化，就可以正确地发送JSON数据了。

urlencoded格式的Ajax请求：

```
$.ajax({
    url: 'some_url',
    type: 'POST',
    data: object
});
```

JSON格式的Ajax请求（需要手动进行JSON）：

```
$.ajax({
    url: 'some_url',
    type: 'POST',
    contentType: 'application/json',
    data: JSON.stringify(object)
});
```

## Node.js的npm包之request

在Node.js环境中，如果使用了流行的npm模块[`request`](ttps://github.com/request/request)，那么这样写是模拟URLEncoded编码进行提交的：

```
request.post('http://service.com/upload', {
    form: {
        key:'value'
    }
});
```

而要想以JSON格式执行POST请求，那么应该这样：

```
request({
    url: '',
    method: 'POST',
    json: true,
    body: {
        key:'value'
    }
});
```
`request`模块对`body`的说明：

> body - entity body for PATCH, POST and PUT requests. Must be a Buffer or String, unless json is true. If json is true, then body must be a JSON-serializable object.

是说，`body`必须是个`Buffer`类型或者`String`类型，除非`json`选项为`true`。如果`json`为真，那么`body`必须是一个可以被JSON序列化的对象。


## 参考资料

[四种常见的 POST 提交数据方式](https://imququ.com/post/four-ways-to-post-data-in-http.html)
