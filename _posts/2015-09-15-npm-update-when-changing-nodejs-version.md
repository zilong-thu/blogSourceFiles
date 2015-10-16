---
title: nodejs版本升级时npm包的更新
date: 2015-09-15 11:06:40
tags: npm
categories: NodeJS

---

最近NodeJS正式发布了V4.0.0版本，整合了之前的node v0.12版本与io.js的最新版，功能有新增，性能有优化，package也会产生不少问题。这里简记一下，以作备忘。

<!-- more -->

### issue 1
第一个问题是在使用Hexo的过程中遇到的：[Hexo is unavailable for Nodejs 4.0.0 #1493](https://github.com/hexojs/hexo/issues/1493#issuecomment-139958344)。错误如下：

```
$ hexo g
Segmentation fault: 11
```

对于这种情形，在博客的工程目录下运行`npm rebuild`确实可以解决我的问题。

### issue 2

第二个问题是`node-inspector`服务无法启动。正常情况下是这样的：

```
$ node-inspector &
[1] 3422
localhost:bf_admin wzl$ Node Inspector v0.12.3
Visit http://127.0.0.1:8080/?ws=127.0.0.1:8080&port=5858 to start debugging.
```

更新到了nodejs v4.0就无法通过`node-inspector &`来启动服务了（它不会告诉你去打开某页面来进行调试）。

这时候，先查看一下全局安装的包有哪些需要更新的（npm会联网检查版本信息）：

```
$ npm outdated -g --depth=0
Package         Current  Wanted  Latest  Location
node-inspector   0.12.2  0.12.3  0.12.3  /usr/local/lib > node-inspector
npm              2.14.2  2.14.3  2.14.3  /usr/local/lib > npm
sequelize-cli     1.7.4   1.9.1   1.9.1  /usr/local/lib > sequelize-cli
```

确实是有比较旧的包，因此全局更新一下：

```
sudo npm install -g
```

而如果只是想更新某个包，那么在上面的命令后面指明Package名字就可以了。更多帮助可以从[npm的官网文档](https://docs.npmjs.com/getting-started/updating-global-packages)里获得。

### 小结

__Point is: 包有问题，就去npm官网看文档，学会用它来管理包。__