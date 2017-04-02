---
title: 关于MacOS文件系统的文件名大小写问题
date: 2017-03-20 13:52:00
tags: 
  - 操作系统
  - 文件系统
categories: 操作系统
---

## 背景

注意到这个事情，是因为今天在工作中发现了由此导致的一个构建问题。我在某个项目A上进行开发，该项目依赖了一个公共组件C。C是由其他同学负责维护，里面的JS源码、以及由 ES6 编译为 ES5 之后的部分文件名，是大写开头的（当然了，我个人是习惯于总是小写命名文件，用连字符处理长文件名情形），但是有个文件在创建的时候，用了小写开头的：`pick.js`，但是在引用的时候，是这样的：

```
import * from './Picker.js';
```

由于我们所有的开发人员都是配 Mac，因此在开发环境中，并没能发现这个隐藏的错误。只是在 A 项目中，发现这个文件是找不到的（我们使用了 webpack 进行构建）：

```
Module not found: Error: Can't resolve './Picker' in 'some-file'
```

最后，通过修改文件名，就解决了这个问题。

<!-- more -->


## 现状

| 操作系统  | 文件系统     | 是否大小写敏感   |
|----------|-------------|----------------|
| Unix     |             | 是             |
| Linux    |             | 是             |
| MacOS    | 日志式 HFS+  | 默认否         |
| Windows  | FAT32 NTFS  | 默认否         |


在 [Is bash in OSX case-insensitive?](http://apple.stackexchange.com/questions/22297/is-bash-in-osx-case-insensitive) 中有一个很好的回答：

> HFS+ (the Mac filesystem) is usually configured to be case insensitive but case preserving. This means that the file system will consider `foo` and `FoO` to be the same, but when you create a new file it will remember which letters where capitalized and which were not.
> 
> To check whether a disk is case sensitive, run for example:
> 
> `diskutil info disk0s2`
> 
> Look for the `Name:` line. If it reads something like `Mac OS Extended (Case-sensitive, Journaled)` it means that it is case-sensitive. If it just reads `Mac OS Extended` (without the Case-sensitive) then it is only case preserving but not case sensitive.

## 小结

要意识到不同操作系统之间是存在这些最基本的差异的。而文件命名，也最好要养成一个习惯，尽量只使用小写。

