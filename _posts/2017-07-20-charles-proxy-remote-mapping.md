---
title: Charles 的配置
date: 2017-07-20 20:09:15
tags: Charles
categories: Charles
---

**目录**

+ HTTPS 抓包配置
+ Charles 请求映射（Proxy Remote Mapping）
+ iOS 设备配置证书

<!-- more -->

## https 抓包配置

点击 Charles 的 Help --> SSL Proxing —> Install Charles Root Certificate：

<img src="/images/2017/07/pic-1-menu.jpg" />

然后在弹出的对话框里选择“添加”按钮，进入钥匙串管理界面：

<img src="/images/2017/07/pic-2-key-chain.jpg" />

双击刚才生成的 charles 证书，在下面的对话框里选择使用此证书时【始终信任】。然后会要求输入系统密码。输入密码确认后，就添加完成了。

<img src="/images/2017/07/pic-3-trust.jpg" />

在 Charles 中启用 SSL Proxy，菜单 Proxy → SSL Proxy Settings。

<img src="/images/2017/07/pic-4-ssl-proxing.jpg" />

点击【Add】，在 Edit Location 对话框里，把 443 端口添加进去。点击OK。

<img src="/images/2017/07/pic-5-443-port.jpg" />


## 请求映射（Proxy Remote Mapping）

选择 charles 的 Tools —> Map Remote，在弹出的 Map Remote Settings 里面点击【Add】：

<img src="/images/2017/07/pic-6-remote-map.jpg" />


## iOS 设备配置证书

通过上面的配置，安卓设备已经可以直接代理 HTTPS 的请求了。但是 iOS 要更为严格一些，需要再给手机配置一下证书。给手机设置代理服务后，打开 Safari 访问 [https://chls.pro/ssl](https://chls.pro/ssl)，根据提示安装证书，之后就可以代理 HTTPS 请求到 HTTP 了。

