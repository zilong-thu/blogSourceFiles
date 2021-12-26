---
title: HTTP 头的中文字符处理案例
date: 2016-12-07 18:05:00
tags: HTTP
categories: HTTP
---

简单记录一下工作中遇到的两个与HTTP&中文字符相关的问题及解决方案。

参考资料[1]总结得非常细致。本文再把相关的 RFC 协议原文贴过来，做笔记用。

<!-- more -->


## 下载文件名含中文而浏览器解析为“乱码”

RFC 2616 规定 HTTP 头必须是 ASCII 编码。

如果文件名含中文，那么在用 Chrome、Safari 时，浏览器可能会自动对下载文件进行一次解码。解码时的处理，可能正确也可能不正确。这一次我碰到的情况就是，在严格按照各种标准来实现的 FireFox 中进行文件下载，文件名含中文，然后保存的文件仍然只是编码后的名字。

提供文件下载服务的程序需要在 HTTP 响应的头里按一定规则设置`Content-Type`和`Content-Disposition`这两个字段：前一个是与文件类型相关的声明，后一个用于指定下载后的文件名以及相应的解码规则。

> Content-disposition 是 MIME 协议的扩展，MIME 协议指示 MIME 用户代理如何显示附加的文件
> [3]

例如：`Content-disposition: attachment; filename=foobar.pdf`。不过，filename如果包含中文字符，那么最好在发给浏览器之前，服务器端程序最好先做一下 `encodeURIComponent`。不过需要注意的是，`encodeURIComponent`与Java 的 `java.net.URLEncoder.encode()` 方法并不一样，可以参考[1]中的代码实现：

```
import java.net.URLEncoder;

/**
 * 符合 RFC 3986 标准的“百分号URL编码”
 * 在这个方法里，空格会被编码成%20，而不是+
 * 和浏览器的encodeURIComponent行为一致
 */
public static String encodeURIComponent(String value) {
  try {
    return URLEncoder.encode(value, "UTF-8").replaceAll("\\+", "%20");
  } catch (UnsupportedEncodingException e) {
    e.printStackTrace();
    return null;
  }
}
```

一个可以工作的下载文件响应的 `Content-Disposition` 首部是这样的：

```
Content-Disposition: attachment;
                     filename="$encoded_fname";
                     filename*=utf-8''$encoded_fname
```

其中，`$encoded_fname` 指的是将 `UTF-8` 编码的原始文件名按照 [RFC 3986](https://tools.ietf.org/html/rfc3986#section-2.1) 进行百分号编码（percent encoding）后得到的。百分号编码（percent encoding）的定义如下：

> A percent-encoding mechanism is used to represent a data octet in a
> component when that octet's corresponding character is outside the
> allowed set or is being used as a delimiter of, or within, the
> component.  A percent-encoded octet is encoded as a character
> triplet, consisting of the percent character "%" followed by the two
> hexadecimal digits representing that octet's numeric value.  For
> example, "%20" is the percent-encoding for the binary octet
> "00100000" (ABNF: %x20), which in US-ASCII corresponds to the space
> character (SP).  Section 2.4 describes when percent-encoding and
> decoding is applied.
> 
>               pct-encoded = "%" HEXDIG HEXDIG
> 
> The uppercase hexadecimal digits 'A' through 'F' are equivalent to
> the lowercase digits 'a' through 'f', respectively.  If two URIs
> differ only in the case of hexadecimal digits used in percent-encoded
> octets, they are equivalent.  For consistency, URI producers and
> normalizers should use uppercase hexadecimal digits for all percent-
> encodings.


## HTTP 头部必须是 ASCII 字符集

项目中遇到一个情况，需要在发往其他服务的 HTTP 请求的头里携带用户名，于是写了个这样的首部：

```
userName: bilibili
```

大部分情况下OK。不过，发现有的用户名是中文……嗯，然后 Node 的 http 模块就开始报字符集不支持的错误。在 stackoverflow 的相似问题中找到了大概的原因：[What character encoding should I use for a HTTP header?](http://stackoverflow.com/questions/4400678/what-character-encoding-should-i-use-for-a-http-header)。高票答案解释说，只有 ASCII 字符是可以确保正常工作的。

HTTP 的规范中如此描述（[3.2.4.  Field Parsing](https://tools.ietf.org/html/rfc7230#section-3.2.4)）：

> Historically, HTTP has allowed field content with text in the ISO-8859-1 charset [ISO-8859-1], supporting other charsets only through use of [RFC2047] encoding. In practice, most HTTP header field values use only a subset of the US-ASCII charset [USASCII]. Newly defined header fields SHOULD limit their field values to US-ASCII octets. A recipient SHOULD treat other octets in field content (obs-text) as opaque data.

即 HTTP 头部的事实字符集乃是 US-ASCII 一个子集，虽然 HTTP 规范允许的字符集是 ISO-8859-1。

> ISO-8859-1 编码是单字节编码，向下兼容ASCII，其编码范围是0x00-0xFF，0x00-0x7F之间完全和ASCII一致，0x80-0x9F之间是控制字符，0xA0-0xFF之间是文字符号。

那么只好把可能包含中文的头部值进行 URI 编码了。

## 参考资料

1. [正确处理下载文件时HTTP头的编码问题（Content-Disposition）](https://blog.robotshell.org/2012/deal-with-http-header-encoding-for-file-download/)
2. [各浏览器下载文件名不乱码的解决办法](https://segmentfault.com/a/1190000005994758)
3. [header中Content-Disposition的作用](http://www.cnblogs.com/hongfei/archive/2012/06/12/2546758.html)


## 附：关于何时需要进行 URI 编码

> 2.4.  When to Encode or Decode
> 
> Under normal circumstances, the only time when octets within a URI
> are percent-encoded is during the process of producing the URI from
> its component parts.  This is when an implementation determines which
> of the reserved characters are to be used as subcomponent delimiters
> and which can be safely used as data.  Once produced, a URI is always
> in its percent-encoded form.
> 
> When a URI is dereferenced, the components and subcomponents
> significant to the scheme-specific dereferencing process (if any)
> must be parsed and separated before the percent-encoded octets within
> those components can be safely decoded, as otherwise the data may be
> mistaken for component delimiters.  The only exception is for
> percent-encoded octets corresponding to characters in the unreserved
> set, which can be decoded at any time.  For example, the octet
> corresponding to the tilde ("~") character is often encoded as "%7E"
> by older URI processing implementations; the "%7E" can be replaced by
> "~" without changing its interpretation.
> 
> Because the percent ("%") character serves as the indicator for
> percent-encoded octets, it must be percent-encoded as "%25" for that
> octet to be used as data within a URI.  Implementations must not
> percent-encode or decode the same string more than once, as decoding
> an already decoded string might lead to misinterpreting a percent
> data octet as the beginning of a percent-encoding, or vice versa in
> the case of percent-encoding an already percent-encoded string.