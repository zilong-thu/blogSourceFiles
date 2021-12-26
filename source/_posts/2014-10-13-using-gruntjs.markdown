---
layout: "post"
title: "用gruntJS压缩合并脚本和样式小记"
date: 2014-10-13 22:52:38
tags: GruntJS
categories: JavaScript
sidebar: false
---

记录自己使用GruntJS对脚本、样式表进行合并及压缩的经验与心得体会。

<!-- more -->

###只使用Uglify.js
最开始，我只用Uglify.js对JS文件进行合并、压缩。

首先，安装uglify.js

```
/* 使用的是1.3.5版本 */
npm install uglify-js@1.3.5
```

然后新建如下JS文件：

``` javascript 命名为compile.js
/* 使用uglify.js版本1.3.5
 * node版本：新的就行
 * 功能：对JS文件进行压缩、合并
 */

var fs  = require('fs'); 
var jsp = require("uglify-js").parser;
var pro = require("uglify-js").uglify;

/* 批量读取文件，压缩之 */

function buildOne(fileIn, fileOut) {
    if (fileIn.length > 0) {
        var finalCode = [];
        var origCode = '';
        var ast = '';
        for (var i = 0,len = fileIn.length; i < len; i++) {
            origCode = fs.readFileSync(fileIn[i], 'utf8');
            ast = jsp.parse(origCode); 
            ast = pro.ast_mangle(ast); 
            ast = pro.ast_squeeze(ast);
            finalCode.push(pro.gen_code(ast), ';');
        };
    }

    fs.writeFileSync(fileOut, finalCode.join(''), 'utf8');
}

/* 批量的话写多个一起执行即可 */
buildOne(['js/main.js', 'js/Panel.js','js/map.js'], 'js/main.min.js');
buildOne(['js/a.js', 'js/b.js'], 'js/ab.min.js');
```

合并、压缩时，运行`node compile.js`即可。

###使用Gruntfile.js
后来，懒得单独去找合并压缩CSS文件的工具，就转向了Grunt。总体来说，Gruntfile.js是一个非常好的静态资源整合自动化解决方案。

<img src="/images/blog/javascript/201411/gruntjs-home.jpg">
<a href="http://www.gruntjs.net/" target="_blank">GruntJS中文网</a>

####搭建步骤：

（1）安装NodeJS

（2）安装 CLI

全局安装。可能需要管理员权限。

```
npm install -g grunt-cli
```

（3）新建package.json，然后执行`npm install`命令

```json package.json
{
  "name": "随便什么名字，一般取项目名",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "~0.4.1",
    "grunt-contrib-jshint": "~0.6.0",
    "grunt-contrib-nodeunit": "~0.2.0",
    "grunt-contrib-uglify": "~0.2.2",
    "grunt-contrib-cssmin": "~0.5.0"
  }
}
```

（4）新建Gruntfile.js，在里面配置要执行的任务（合并、压缩等）

（5）在Gruntfile.js的同一目录下，运行`grunt`命令，就能得到想要的压缩文件。记得将项目中相应的资源引用更改为压缩后的文件名即可。