---
title: 初探微信小程序开发
date: 2018-01-13 09:41:12
tags: 小程序
categories: Web-FE
---

## 背景

+ 项目起始时间：2017-11-28
+ 前端人数： 3
+ 页面数： 6
+ 一期上线时间：2018-01-16

在我们开发的过程中，小程序的生态也不断变化。例如 `<web-view>` 最开始不支持其打开的 web 页面调起小程序页面，后来则新增了 JS-SDK 并在高版本的基础库里支持了这样的功能；再例如它的 IDE 不断优化升级，开发体验变好；页面的5级跳转限制，改为了10级；小程序体积限制由 1MB 扩大为了 2MB …… 总体来说，一直在朝着好的方向发展。

<!-- more -->

## web-view 的使用

应当指定一个特定的路径，用于打开任意的在业务域名白名单内的页面。

<img src="/images/2018/01/wxapp-dir.png" style="width: 200px;" alt="页面目录">

由于 `<web-view>` 所在页面只会渲染 web 页面，其他任何组件都不显示，所以，在相应的 HTML 里就只包含这样的一个组件即可：

```
<web-view src="{{url}}"></web-view>
```

`web-view/index.js` 的主要逻辑如下：

```javascript
Page({
  data: {
    url: '',
  },
  onLoad() {
    const sysInfo = wx.getSystemInfoSync();
    const version = new Version(sysInfo.SDKVersion);

    // web-view 组件是 1.6.4 基础库开始支持的
    // 但是其 JS-SDK 却是从 1.6.6 才开始支持
    // 所以这里要求最低为 1.6.6
    if (version.isLowerThan('1.6.6')) {
      return wx.showModal({
        title: '提示',
        content: '当前微信版本过低，无法在小程序内打开网页，请升级到最新微信版本后重试。',
        showCancel: false,
        confirmText: '知道了',
        success() {
          wx.navigateBack();
        }
      });
    }

    // 可以拿到 URL 里的查询参数
    if (options && options.__host) {
      let url = decodeURIComponent(options.__host || '');
      const query = Object.assign({}, options);
      delete query.__host;

      url += '?' + util.stringifyQuery(query);

      this.setData({url});
  }
})
```

其他页面在想使用 web-view 时就可以这样，方式1：

```html
<navigator url="/pages/webview/index?__host=https://your.host.name/path/to/page&query_1=value_1&query_2=value_2">
  通过web-view打开网页
</navigator>
```

或者方式2:

```javascript
const url = 'https://your.host.name/path/to/page?query_1=value_1&query_2=value_2';
wx.navigateTo({
  url: '/pages/webview/index__host=' + encodeURIComponent(url)
});
```

## 数据缓存

可以比较充分地使用小程序的 `storage` 接口，进行本地缓存。建议缓存的数据包括：

+ 元数据
+ 用户登录后的基础数据，例如 userId，token
+ 设备ID

## 图片资源

由于小程序的样式表里无法使用相对路径引用图片文件，所以很多时候只好使用 base64 进行转码。

## 常用的类

+ 用于网络请求的 request.js，支持 Promise 接口
+ 版本判断，Version.js
+ 时间日期格式化，类似于 moment.js，但是要更为轻量级
+ URL 解析与序列化，url.js

## 兼容性

iOS 与 Android 平台下，即便相同的小程序，也有许多细节上的差异。在进行兼容性测试时需要按照 H5 的平台兼容性进行测试。

此外，还有微信各版本的兼容性测试。前端要基于 Version.js 判断微信小程序 SDKVersion，以便对低版本进行友好的提示。

## `<web-view>` 的 user-agent

user-agent 对于我们开发者来说比较方便，尤其在一些做服务端渲染的页面，以及其他对用户的运行环境敏感的服务。

如果期望在 user-agent 里用 `miniprogram` 标识为小程序的浏览器，那么恐怕会在 iOS 下遇到问题：其微信小程序 `<web-view>` 的 UA 里缺失了 miniprogram 字段。用 `<web-view>` 打开任意页面，获取其发起的请求里携带的 user-agent，拦截到的各个客户端的 user-agent 如下：

① dev-tools:

Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1 wechatdevtools/1.01.1712150 MicroMessenger/6.5.7 Language/zh_CN webview/1_3 webdebugger miniprogram port/9974

② android wechat:

Mozilla/5.0 (Linux; Android 7.0; Mi-4c Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/53.0.2785.49 Mobile MQQBrowser/6.2 TBS/043632 Safari/537.36 MicroMessenger/6.6.1.1220(0x26060135) NetType/WIFI Language/zh_CN MicroMessenger/6.6.1.1220(0x26060135) NetType/WIFI Language/zh_CN miniProgram

③ iphone6:

Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_1 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Mobile/15C153 MicroMessenger/6.6.1 NetType/WIFI Language/zh_CN

④ iphone7:

Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_3 like Mac OS X) AppleWebKit/603.3.8 (KHTML, like Gecko) Mobile/14G60 MicroMessenger/6.6.1 NetType/WIFI Language/zh_CN

所以目前来看，ios 微信的实现是有问题的。

官方对于此问题的回应是：

> userAgent暂不支持，后续会提供类似功能

所以，只能耐心等待了。

