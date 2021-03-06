---
title: Webpack 指南（1. 入门篇）
date: 2017-04-23 23:40:00
tags: 
  - Webpack
  - 前端工程化
categories: 
  - Webpack
banner: /images/2017/04/webpack-logo.png
---


## webpack 基本概念

[webpack](https://webpack.js.org/) 是目前非常流行的前端资源（JS/CSS/IMG/...）打包解决方案。它默认按照 ES2015 定义的模块机制来查找、打包文件，即对 `import`、`export` 声明进行相应的模块封装，而对于其他代码，则不做任何改动。如果想使用其他的 ES6 特性，现阶段需要借助于 [Babel](https://babeljs.io/) 这样的编译工具。

<!-- more -->

## 源码目录组织

### B 端项目

首先，我们以 B 端多页面项目为例，说一下一个合理的前端工程项目的目录结构，怎么组织比较合适——所谓合适，是指代码目录清晰（URL清晰）、可扩展、公有/私有资源区分明确，等等。

在远古时代，开发人员是根据资源的类型来划分目录的，对于任何一个页面，传统的组织方式可能是这样的，就三个目录：

```
.
├── js
|    ├── page1.js
|    └── page2.js
├── css
|    ├── page1.css
|    └── page2.css
├── img
|    ├── page1_dir
|    └── page2_dir
├── index.html
```

但是随着对前端开发效率的更高追求、前端应用复杂度的提升，以及开发人员们对前端组件化的逐渐认可，这样的目录结构已远远不能满足需求。参考：

+ [Web应用的组件化（一）——基本思路](https://github.com/xufei/blog/issues/6)

主要原因是，无论是 JS 文件，还是 CSS/IMG，它们都是静态资源，都是可以预先编译或转义、压缩、文件合并等处理好了，推到服务器上，让浏览器下载的。那么对于 web 应用来说，不需要加载源码，只加载若干主要入口，然后剩余资源由组件去自行发起请求加载即可。




## 基本使用


## 单页面


## 多页面

考虑下面的多页面应用：

+ 极少的 SSR（Server Side Render，服务器端渲染）
+ 所有的页面共享少数几个甚至只有一个 HTML 模板，UI 渲染完全在浏览器端加载完 JS 后再执行
+ URL 的设计与静态文件服务器类似

项目的目录结构如下：

```
├── build
├── client
│   ├── components
│   ├── pages
│   │   ├── explore
│   │   ├── home
│   │   │   ├── index.js
│   │   │   └── style.less
│   │   └── user
│   └── vendor
├── package.json
├── server
└── webpack.config.js
```

