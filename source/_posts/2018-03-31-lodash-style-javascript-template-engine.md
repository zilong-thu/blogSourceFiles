---
title: Lodash 风格的 JavaScript 模板引擎
date: 2018-03-31 18:49:30
tags: JavaScript
categories: JavaScript
---

## 参考文章

最早看到这个技术是在这篇文章里 [JavaScript template engine in just 20 lines](http://krasimirtsonev.com/blog/article/Javascript-template-engine-in-just-20-line)。原理也分析得非常清晰。

<!-- more -->

## 我的实现

假设我们已经有了这样的模板字符串：

```javascript
var TPL = `<div><% this.name %></div>
  <div><% this.age %></div>
  <ul>
  <% for (var i = 0; i < 3; i++) { %>
    <li><% i %></li>
  <% } %>
  </ul>
`;
```

模板引擎的第一步，是设计中间代码。我们希望这样的字符串会被编译为这样的字符串（该字符串将用于生成函数体）：

```javascript
`var r = [];
r.push("<div>");
r.push(this.name);
r.push("</div>  <div>");
r.push(this.age);
r.push("</div>  <ul>");
for (var i = 0; i < 3; i++) {
r.push("<li>");
r.push(i);
r.push("</li>");
}
r.push("</ul>");
return r.join("");`
```

实现：

```javascript
function compile(tpl, data) {
  var code = 'var r = [];\n';
  var cursor = 0, match;

  // 要注意对 <%%> 之间的代码段使用捕获
  var reg = /<%([^%>]*)%>/g;

  var add = function(line, isJS) {
    // 清除模板每行的首尾空格
    line = line.trim();

    // 换行符、回车符、制表符也都删除
    var res = 'r.push("' + line.replace(/[\r\t\n]/g, '') + '");\n';

    var isControlBlock = /({|}|if|for|while|switch|case|break|continue)/.test(line);
    if (isControlBlock) {
      // 如果某行里包含了控制逻辑代码，那么在剃掉 <%%> 后，代码直接执行即可，不必 push 到一个数组中
      res = line + '\n';
    } else if (isJS) {
      // 如果是其他的（取右值的表达式），那么 push 到数组中
      res = 'r.push(' + line + ');\n';
    }

    code += res;
  }

  while(match = reg.exec(tpl)) {
    add(tpl.slice(cursor, match.index));
    add(match[1], true);
    cursor = match.index + match[0].length;
  }

  add(tpl.substr(cursor, tpl.length - cursor));

  code += 'return r.join("")';
  console.log(code, '\n');

  var func = new Function(code);
  return func.call(data);
}
```

测试：

```javascript
var res = compile(TPL, {name: 'haha', age: 23});
console.log(res);

// 输出： <div>haha</div>  <div>23</div>  <ul><li>0</li><li>1</li><li>2</li></ul>
```

## Lodash.js 的实现