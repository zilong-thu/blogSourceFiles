---
title: NodeJS+Express+MySQL开发小记(1)
date: 2015-04-14 23:40:56
tags: NodeJS
categories: NodeJS
sidebar: false
---
最近使用NodeJS 0.12.0 + Express 4.12.2 + MySQL 5.6 来做一个完整的社区网站，纯属个人项目，网站内容暂不透露。如果自己能坚持下去（希望如此），那么在基本成型之后，会考虑开放源代码，并将网站上线运行。也许从此就可以赢娶白富美出任CEO登上人生巅峰了呢。

总而言之，现在要扩展自己的知识面。__Aiming to be a Full-Stack Engineer!__

这个项目，姑且代号为__MEN__吧（MySQL Express Node），是从2015年3月28日开始创立的。下面简单记录一下开发中的关键技术问题。

<!-- more -->

## 项目的包描述
首先是关于工具的选择。选择MySQL而不是貌似很火的MongoDB，是因为听说了后者在尽力实现关系型数据库的特性，因此觉得还是从MySQL入门比较好（是的，哥完全没有数据库基础）。

然后服务器的模板引擎用的是ejs，而不是默认的jade，原因是，jade风格过于迥异，用ejs便于以后自己阅读。

关于前后端分离，只有做了后端开发，才是有所体会。视图（View）的部分，服务器可以渲染，浏览器也可以渲染。从性能、开发效率等各方面来考虑，确实把视图交给前端更好。之后的开发中要注意这个问题。

npm的`mysql`模块是用来连接数据库的，现在感觉用起来还不错。

```JSON package.json
{
  "name": "men",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.12.0",
    "compression": "^1.4.3",
    "cookie-parser": "~1.3.4",
    "debug": "~2.1.1",
    "ejs": "~2.3.1",
    "express": "~4.12.2",
    "express-session": "^1.10.4",
    "less-middleware": "1.0.x",
    "moment": "^2.10.2",
    "morgan": "~1.5.1",
    "mysql": "^2.6.1",
    "serve-favicon": "~2.2.0"
  }
}
```

## 在Node中独立出MySQL连接池模块

最初的时候，写了`user`, `topic`两个模型（MVC中的Model）。每个模块文件中都各自创建数据库连接池。然后就发现服务器在启动的时候比较耗时了。后来又添加了一个`website`模型（用来获取网站运行状态的），也是独自创建并使用数据库连接池。结果服务器启动慢得无法忍。

上面的每个模块各自创建并使用连接池的做法自然非常之不可取。完全违背了pool的最初设计宗旨。解决方法也很简单，把数据库连接池的创建放到一个单独的模块里，然后把创建后的pool对象暴露出来，即可。

以下是代码。

```javascript  /module/pool.js
var mysql = require('mysql');

var pool = mysql.createPool({
    host: 'localhost',
    port: 3306,
    database: 'database-name',
    user: 'username',
    password: 'password'
});

module.exports = pool;
```

然后如果某个其他的模块需要使用数据库连接池，就引用一下：

```
var pool = require('../modules/pool.js');
  
pool.getConnection(function(err, connection) {
    if (err){
        /* handle error  */
    }
    
    connection.query({
        sql: 'SELECT COUNT(*) AS user_count FROM t_user'
    }, function(err, rows, fields) {
        if (err){
            /* handle error  */
        }
        connection.release();
        callback(null, rows);
    });
});
```

这样才是正确的连接池用法。

## 关于Promise
虽然目前写的代码量还不大，但是已经对NodeJS的异步回调深恶痛绝了。发现目前使用的Node 0.12.0是支持原生Promise的，于是不用白不用。不过理解起来还挺困难。

现在要解决这样的案例：先从数据库t_user表中获得注册用户数，然后从t_topic表中查询话题总数，最后把它们交给回调函数（嗯，是的，还是用了一下回调函数~~）。

代码暂时是这样写的：

```javascript
exports.getWebsiteStatus = function(callback){
    var promise = new Promise(function(resolve, reject){
        pool.getConnection(function(err, connection){
            if (err) {
                reject(err);
            }else{
                connection.query({
                    sql: 'SELECT COUNT(*) AS user_count FROM t_user'
                }, function(err, rows, fields) {
                    if (err){
                         reject(err);
                    }else{
                        resolve(rows);
                    }
                    connection.release();
                });
            }

        });
    });

    promise.then(function(rows1){
        console.log('success');
        pool.getConnection(function(err, connection){
            if (err) {
                callback(err);
            }else{
                connection.query({
                    sql: 'SELECT COUNT(*) AS topic_count FROM t_topic'
                }, function(err, rows2, fields) {
                    if (err){
                        callback(err);
                    }else{
                        callback(null, {
                            user_count: rows1[0].user_count,
                            topic_count: rows2[0].topic_count
                        });
                    }
                    connection.release();
                });
            }

        });
    }, function(err){
        callback(err);
    });
};
```

关于如何改进，寡人猜测也就两方面：（1）直接用SQL进行两次查询，不然时间会耗费在获取数据库的连接上；（2）Promise这么写也太丑了，得接着研究如何优雅地写Promise。
