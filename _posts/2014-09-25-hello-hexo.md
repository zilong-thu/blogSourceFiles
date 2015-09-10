---
title: "Hello, hexo"
date: 2014-09-25 21:31:06
tags: Hexo
categories: 博客搭建
sidebar: false
---
今天把博客从Octopress迁移到了Hexo之下。博客的生成速度从此就跟飞一样了。Ruby这么慢的东西，实在不能用来编译博客。

撒花庆祝！！！

<!-- more -->

###Intall Hexo
安装Hexo时使用的package.json文件内容如下：

```json package.json文件
{
  "name": "hexo-site",
  "version": "2.8.3",
  "private": true,
  "dependencies": {
    "hexo-renderer-ejs": "*",  /* ejs解析器 */
    "hexo-renderer-stylus": "*",  /* stylus解析器 */
    "hexo-renderer-marked": "*",
    "hexo-generator-sitemap": "*",  /* 用于生成sitemap.xml */
    "hexo-generator-feed": "*"  /* 生成atom.xml之用 */
  }
}
```

###几个常用的Hexo命令
写新的博文：
```
hexo new Category "hello"
```


生成博客：
```
 hexo generate
```

将博客发布到gitpages，需要输入github的用户名和密码
```
 hexo deploy
```

在本地预览博客，也许需要删除db.json才可以看到修改顶级配置文件后的结果

```
 hexo server
```

###主题
现在使用的主题是根据 <a href="https://github.com/chenall/hexo-theme-chenall" target="_blank">chenall v2.2 </a> 改造而来的。布局沿用了该主题，但是颜色我进行了darken改造，主要是为了保护视力，以及，出于对Sublime Text编辑器的喜爱。

###优化
移除了新浪微博秀、有言等一系列花哨的部件；整合了一下CDN资源，网站统计从google换成了百度统计；等等一系列优化，使得目前加载速度比最开始大有提升。

###使用百度统计的一点小提示
最开始使用了同步加载的代码，有时候百度会抽风，导致页面渲染阻塞，非常之不爽。非常推荐大家使用异步加载的方式安装统计代码。百度对此的说明是：

> 百度统计异步代码是以异步加载形式加载了网站分析代码，使用该代码能够大幅提升您网站的打开速度（目前使用百度统计异步代码会导致百度统计图标和代码检查功能的失效）

代码大概是这样子的：

```javascript
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "//hm.baidu.com/hm.js?73ca1d91f31b2463befdc1c1827f2576";
  document.body.appendChild(hm);
})();
```

####CDN的使用
theme最初的CDN都是使用bootcdn，由于某种原因，时常会很久才获取到资源。于是转用百度的CDN公共库，效果还不错。

<a href="http://developer.baidu.com/wiki/index.php?title=docs/cplat/libs" target="_blank">百度公共CDN介绍页面</a>

Bootstrap是基于3.1.0版本的，百度的公共CDN只有3.0.3版本的，但是二者貌似区别不大，对我的博客基本没有影响。