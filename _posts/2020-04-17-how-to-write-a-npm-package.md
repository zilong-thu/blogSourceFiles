---
title: 开发一个 npm 包的大概流程
date: 2020-04-17 16:38:00
tags: NodeJS
---

入门级流程知识。父级知识节点：NodeJS。

<!-- more -->

## 第一步: 初始化项目

包括两部分：

```
$ npm init
$ git init
```

最主要的是在 `package.json` 里通过 `main` 字段定义这个包对外暴露的入口；如果是提供命令行工具，则需要通过 `bin` 字段来定义暴露的命令名称与实际执行的文件。例如：

```json
{
  "name": "@qcsfe/qbear",
  "version": "2.8.0-5",
  "description": "一个基于 Webpack 的打包构建工具，定义清晰的输入输出流",
  "main": "index.js",
  "scripts": {
    "test": "mocha tests/index.js",
    "lint": "eslint --ext .js ./lib"
  },
  "bin": {
    "qbear": "cli.js"
  },
  "author": "wangzilong03",
  "license": "MIT",
  "dependencies": {}
}
```

## 第二步: 在全局注册包名

一个尚未发布的 npm 包，其他项目是无法通过 npm/yarn 来安装它的，但有两种方式可以让项目在本地使用。

### 第一种：相对路径依赖

如果我们的项目 A，希望使用一个尚未发布的包 `newPackage`，可以通过 `file` 前缀告诉项目 A 从本机读取依赖：

```json
{
  "dependencies": {
    "@babel/polyfill": "^7.4.0",
    "@qcsfe/qbear": "2.8.0-5",
    "newPackage": "file:path/to/newPackage",
  }
}
```

### 第二种：软链接

首先，在我们的包 `newPackage` 下面，执行 `npm link`，这样会在全局注册自己的包名。

然后在需要使用 newPackage 的项目里执行 `npm link newPackage`，就可以直接用了。

## 第三步：发包

开发、测试中也可以发包，这时候一般不发布语义化的版本号，而是使用诸如 `1.1.1-0`、`1.1.1-alpha`、`1.1.1-SNAPSHOT` 这样的版本号。

```bash
# 先登录
$ npm login

# 发布
$ npm publish
```
