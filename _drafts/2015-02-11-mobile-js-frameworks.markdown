---
title: 移动端JS框架初探
date: 2015-02-11 09:47:00
tags: JS框架
categories: JavaScript
---
移动端的前端开发热度已经不容置疑地在持续走高了。以后要多关注Mobile Front End Coding了。

这里记录几个移动端的开发框架。我看过源码的会详细介绍。
<!-- more -->
###ZeptoJS

[ZeptoJS](http://zeptojs.com/)的最初目标在移动端提供一个精简的类似jquery的js库。其声称自己的大部分API与jQuery是一样的。

另有[中文版API](http://www.css88.com/doc/zeptojs_api/)。

很赞的是它不支持老旧浏览器：IE版本<10的都不支持。

<img src="/images/blog/2015/02/zepto_intro_03.jpg" style="width: 360px; margin: 0 auto;">

####自定义的Building
最好是去[GitHub下载其源码](https://github.com/madrobby/zepto)，然后根据自己的实际需求，参考各模块的功能说明，自定义building模块，例如：

```
/* 先安装所需的node.js包 */
D:\GitHubProjects\zepto-master>npm install
coffee-script@1.5.0 node_modules\coffee-script

shelljs@0.1.4 node_modules\shelljs

uglify-js@2.4.16 node_modules\uglify-js
├── uglify-to-browserify@1.0.2
├── async@0.2.10
├── source-map@0.1.34 (amdefine@0.1.0)
└── optimist@0.3.7 (wordwrap@0.0.2)

express@3.1.2 node_modules\express
├── methods@0.0.1
├── fresh@0.1.0
├── range-parser@0.0.4
├── cookie-signature@1.0.0
├── buffer-crc32@0.2.5
├── cookie@0.0.5
├── commander@0.6.1
├── mkdirp@0.3.5
├── send@0.1.0 (mime@1.2.6)
├── connect@2.7.5 (pause@0.0.1, buffer-crc32@0.1.1, bytes@0.2.0, qs@0.5.1,
rmidable@1.0.11)
└── debug@2.1.1 (ms@0.6.2)

/* 设置自己所需的模块 */
D:\GitHubProjects\zepto-master>SET MODULES=zepto event data ie detect touch

/* 将其整合发布到目录dist下*/
D:\GitHubProjects\zepto-master>npm run-script dist

> zepto@1.1.6 dist D:\GitHubProjects\zepto-master
> coffee make dist

/* 得到的文件 */
dist/zepto.js: 52.8 KiB
dist/zepto.min.js: 23.9 KiB
dist/zepto.min.gz: 8.8 KiB
compression factor: 5.0
```

###SwiperJS | S6

[SwiperJS](http://www.idangero.us/sliders/swiper/)

> Swiper - is the free and ultra lightweight mobile touch slider with hardware accelerated transitions (where supported) and amazing native behavior. It is intended to use in mobile websites, mobile web apps, and mobile native apps. Designed mostly for iOS, but also works great on Android, Windows Phone 8 and modern Desktop browsers. Swiper is created by [iDangero.us](http://www.idangero.us/)

实际上，SwiperJS连同下面的Framework7都是 iDangero.us 的产品。SwiperJS只是其幻灯片解决方案之一，还有其他的，例如[S6](http://www.idangero.us/sliders/s6/)：

> __iDangero.us S6__ - is the premium ultra lightweight 3D touch slider for mobiles with hardware accelerated transitions (where supported), amazing 3D animation and native behavior. It is intended to use in mobile websites, mobile web apps, and mobile native apps. Designed mostly for iOS, but also works on latest Androids and latest Desktop browsers with support of 3D Transforms. S6 is created by [iDangero.us](http://www.idangero.us/)

###Framework7
主要是针对iOS系统的应用开发，可以以web app的形式，也可以利用PhoneGap打包为naitive app。

> [Framework7](http://www.idangero.us/framework7/) - is a free and open source mobile HTML framework to develop hybrid mobile apps or web apps with iOS native look and feel
>
> The main approach of the Framework7 is to give you an opportunity to create iOS apps with HTML, CSS and JavaScript easily and clear. 
>
> __Framework7 is definitely for you if you decide to build iOS hybrid app (PhoneGap) or web app that looks like and feels as great native iOS apps.__

###AmazeUI
[AmazeUI](http://amazeui.org/)是“中国首个开源 HTML5 跨屏前端框架。”

<img src="/images/blog/2015/02/mascot.png" style="width: 360px; margin: 0 auto;">