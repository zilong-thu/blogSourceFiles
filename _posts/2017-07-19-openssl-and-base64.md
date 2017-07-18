---
title: OpenSSL and Base64
date: 2017-07-19 01:06:35
tags:
  - Linux
  - base64
  - OpenSSL
categories: Linux
---

记录一个小命令，涉及到两个比较基础的概念：OpenSSL 和 base64 编码。

> OpenSSL 包含一个命令行工具用来完成 OpenSSL 库中的所有功能，更好的是，它可能已经安装到你的系统中了。
> —— 百度百科
> 
> Base64是一种任意二进制到文本字符串的编码方法，常用于在URL、Cookie、网页中传输少量二进制数据。
> —— [廖雪峰的官方网站 - base64](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001399413803339f4bbda5c01fc479cbea98b1387390748000)

OpenSSL 可以将任意 PNG 图片编码成 base64 字符序列，下面的命令以 `in.png` 图片作为输入，转码后把 base64 序列输出到 `out.txt` 文件里。

```
openssl base64 -in in.png -out out.txt
```

不过有个问题，上面的命令会产生折行（即 line breaks），要想直。要想去掉折行，需要添加 `-A` 参数：

```
openssl base64 -A -in in.png -out out.txt
```
