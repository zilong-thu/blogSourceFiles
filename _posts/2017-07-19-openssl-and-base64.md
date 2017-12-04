---
title: OpenSSL and Base64 以及网站性能优化的思考
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

## 单个文件处理

OpenSSL 可以将任意 PNG 图片编码成 base64 字符序列，下面的命令以 `in.png` 图片作为输入，转码后把 base64 序列输出到 `out.txt` 文件里。

```
openssl base64 -in in.png -out out.txt
```

不过有个问题，上面的命令会产生折行（即 line breaks），要想直。要想去掉折行，需要添加 `-A` 参数：

```
openssl base64 -A -in in.png -out out.txt
```

## 文件批量处理

可以在需要进行批量处理的目录下运行下面的脚本，会将所有的图片逐个生成为同名的 `txt` 文件。

```bash
for filename in `find . -name "*.png" -o -name "*.jpeg" -o -name "*.jpg" -o -name "*.gif"`;
  do openssl base64 -A -in $filename -out $filename.txt;
  done
```

## 使用

```css
background-image: url(data:image/png;base64,base64_code_goes_here);
```

## 多小的图片可以考虑使用 base64 进行内联？

Base64 生成的文件体积为原始图片体积的4/3倍，它对于网站性能优化的提升通常仅针对超小体积的图标类型图片，以体积的稍微增加，来减少 HTTP 的请求次数。那么多小的图片可以考虑使用 base64 进行内联呢？

考虑一个对图片文件的 HTTP 请求报文，其由三部分组成：请求行，请求头，请求响应。例如下面是对一张图片的请求的请求行与请求头报文：

```http
# Request Headers
GET /piaofang/img/connect/project-bg/bg-00-wide-e4a26031.png?__sprite__ HTTP/1.1
Host: ms0.meituan.net
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1
Accept: image/webp,image/apng,image/*,*/*;q=0.8
Referer: http://ms0.meituan.net/piaofang/css/celebrity/piazza.331e5210.css
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,ja;q=0.7,zh-TW;q=0.6,da;q=0.5
```

而得到的响应则是：

```
HTTP/1.1 200 OK
Date: Mon, 04 Dec 2017 08:59:58 GMT
Content-Type: image/png
Content-Length: 4331
Connection: keep-alive
Server: Tengine
Last-Modified: Mon, 04 Dec 2017 06:44:55 GMT
ETag: "5a24eee7-10eb"
Accept-Ranges: bytes
Cache-Control: max-age=2592000,s-maxage=3600
Vary: Accept-Encoding
Access-Control-Allow-Origin: *
X-Ser: BC208_dx-lt-shandong-qingdao-2-cache-7, BC14_lt-fujian-fuzhou-1-cache-1
```

请求头 + 请求响应报文，一共 939 字节（记为H）。再加上图片本身的体积，记为 I 字节。图片转为 base64 后体积会变为 `4/3 * I` 个字节。如果要问对于多大的 I，应该采用 base64 进行转换，就相当于求解满足下面条件的不等式：

```
H + I > 4/3*I
```

解为 `I < 3 * H = 2817KB`，即当图片的体积小于 2.8KB 时，使用 base64 进行压缩是会让性能更优的。而且考虑到 HTTP 请求本身的开销（计算资源、建立连接、网络延时等），将这个上限定为 4KB 也是不错的选择。
