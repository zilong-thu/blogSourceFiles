---
title: 简历
date: 2015-08-09 10:49:42
sidebar:
---

<div class="avatar">
<img src="myResumeAvatar.jpg" style="max-width: 128px;">
<div style="padding-top: 20px; font-weight: bold; text-align: center;"><a href="/about/en">[ English Version ]</a></div>
</div>

<link rel="stylesheet" type="text/css" href="./index.css">

### 基本信息

+ 王子龙 [男]
+ 籍贯：山东省沂水县
+ 户籍：江苏省南京市
+ 出生年月：1989-07
+ 现居地：北京市海淀区
+ E-mail： wangzilong-thu@qq.com
+ Github: https://github.com/zilong-thu/
<li class="telephone">电话：17343008903</li>

### 教育经历

+ 2011-08 ~ 2014-07 硕士：清华大学航天航空学院，力学专业
+ 2007-08 ~ 2011-07 本科：清华大学航天航空学院，工程力学与航天航空工程专业

### 职业发展经历

+ **2022.7 ~ 今，字节跳动·飞书，资深前端工程师**
  - 2022.7 起，任职飞书·IM 前端资深工程师，主要负责 IM 复杂业务逻辑缺陷/oncall治理，PC/WEB同构专项开发。

+ **2018-04 至 2022.6，美团·网约车·打车技术部·终端研发组，资深前端工程师**
  - 2020.10 起至 2022.6，担任美团·网约车·打车技术部·乘客营销前端组技术负责人。团队规模：6 + 1 人。负责团队技术项目规划、业务需求管理、下属成长发展与绩效管理、团队技术与学习氛围建设。技术项目包括H5 标准桥重构与迭代；前端性能优化；乘客活动经营工作台项目迭代与维护；业务指标体系建设；TypeScript 推广普及与代码重构；图片服务维护与迭代（Node + `sharp.js`）等。
  - **H5 标准桥重构与迭代**，目标是在页面、组件中使用统一的 lib 来调用各种基础 API，该 lib 会判断当前所在容器（美团APP、点评APP、美团打车APP、各类小程序、微信H5等等），然后对统一的 API 进行封装实现。例如定位模块（提供 `getLocation` 方法），分享模块（提供 `share`、`configShare`），session 模块（提供 `getUserInfo`、`login`、`logout` 等方法）等等。
  - **营销侧前端性能优化**，主要从下面几个角度切入：
    - 精简体积。利用 webpack-bundle-analyzer 分析构建结果，去掉不用的，或者把不必要打到公共包的文件进行加载顺序调整。去除重复声明或引入的 CSS 代码。充分利用 `webp` 格式图片。
    - 流程控制优化。避免串行，尽量并行。
    - 充分利用缓存。
  - **乘客活动经营工作台**，vue。大规模 TS 化。支持部分活动页面实时预览。
  - **营销活动 H5 页面**（vue, koa），负责了充值返现、红包 PK、套餐商城、助力活动、拼手气分享红包等关键业务的开发。最大的收获是对美团支付流程有了较深入的了解。
  - 担任 **美团打车营销号小程序** 负责人，集成了美团用户登录、统计上报等功能；有一个主要页面：拼团助力。同时，自己还与后端同学一起推动了助力活动的配置后台的产品设计、开发实现，这样运营同学就可以非常灵活地上下线活动以及配置不同的页面样式。上线后受到运营同学的好评。
  - **美团智行码小程序** 核心开发人员之一。自己负责了小程序项目的初始搭建过程，并且对支付、登录、web-view 等基础能力进行了封装。这个小程序包括两个主要业务，扬招服务和防疫服务（扫码绑定 POI、扫码登记等功能）。自己参与了防疫服务的部分需求开发。
  - **前端团队公共基础构建工具 `qbear@2.*` 开发负责人**。`qbear` 针对多页面应用打造，具有动态 entry、构建结果上传 CDN 并可进行回源检测、支持多种开发框架等特性。自己负责在 v1 的基础上进行重构与新功能的开发，更新了 webpack、babel 等依赖，使用了新的方案实现动态 entry 功能，基于 `memory-fs` 封装了 `universal-file-system`（统一内存文件系统）对开发环境下的中间文件进行存储（以供 webpack 使用），新增了回源检测与重试机制，等等。
  - **担任组内多位新人的导师**。共计担任了 3 位新人的导师。都顺利晋升到下一职级。
  - **参与前端团队社招/校招技术面试**。三年时间，面试了 90 多人，最终社招、校招各有 2 人入职。总结输出了社招一面题库、二面指南以及校招流程指南等帮助文档。
  - 在团队中进行了多次分享，包括《Acorn.js 原理与应用》《2018 杭州 Vue Conf 大会参会心得》《<货币金融学>读书分享》。

+ **2015-12 至 2018-04，美团·猫眼电影，高级前端工程师**
  - 这是我第一份互联网公司的工作，自己从一个小菜鸟，迅速地成长为可独当一面的标准程序员，也能够带领多人小组一起维护多个前端系统、完成具有挑战性的任务。离开时，担任猫眼行业服务服务web前端主要负责人（5人前端小组）。
  - 负责的 B 端项目：**商户系统（[e.maoyan.com](https://e.maoyan.com)）**；宣发系统；BD 门户；内部管理平台。其中最主要的项目是商户系统，与自己的导师一起对此项目进行了前后端分离，按照 SPA 进行开发（`react` + `react-router`）；自己对这个系统下几乎每个页面都非常熟悉，考虑到许多业务形态有了变化，以及希望将老的技术栈（`flux` 体系）从代码中移除以降低业务代码复杂度，所以自己对它们进行了大量的重构（包括页面 UI 的优化）。
  - 负责的 H5 项目：猫眼专业版 APP 内嵌 web 页面**影视人相关业务**（[piaofang.maoyan.com](https://piaofang.maoyan.com/celebrity/piazza)）；猫眼电影m端点播院线业务（[m.maoyan.com](https://m.maoyan.com/)）
  - 负责的微信小程序项目：**影视人名片小程序**。负责小程序相关技术的调研、开发阶段的技术难点攻关以及前端开发进度掌控。业务上的主要收获是较深入地了解了美团用户登录体系。
  - 参与前端团队招聘面试、新人培养等公共事务。累计面试 50 多人。共指导过 2 名实习生（一名因读研原因未能留下，另一位在毕业后来到公司工作）。

+ **2014-07 至 2015-11，北方信息控制集团有限公司（NICG，南京）**
  - 负责 Web 前端开发及 Node.js 相关开发

+ **This is how things got started**
  - 在 2013 年的 6 月，读研究生期间，自己借着参与实验室的一个网站项目的机会，开始了 web 前端开发之路。
  - 当时使用了 jQuery

### 技能

+ 主要编程语言： HTML, CSS, JavaScript, TypeScript
+ 熟悉的前端库/框架： jQuery, Bootstrap, Lodash.js, React.js, Vue.js, ECharts.js, 微信小程序
+ 熟悉的服务器端技能/框架：Node.js, Express.js, Koa.js
+ 熟悉的前端构建工具：Gulp, Webpack, Sass/Less/PostCSS
+ 熟悉的工具：Git, SublimeText, VSCode, Charles, Whistle
+ 熟悉的操作系统：Mac OS, Linux
+ 略懂的技能： MySQL/SQL, C/C++, Java, Docker, Angular.js
+ 略懂的软件： Adobe Premiere/ Photoshop/ Illustrator, Vi(m)

### 写作

+ 常年维护并更新博客，[https://borninsummer.com](https://borninsummer.com/)
+ 电子书（有心情就写一点）：[学习正则表达式-基于JavaScript](https://borninsummer.com/learn-regexp-with-javascript/)
+ 电子书（有心情就写一点）：[Web前端工程化与组件化开发实践](https://borninsummer.com/Practice-in-Front-End-Engineering-and-Components-Development/)

### 自我评价

+ 在团队中总能较快成为骨干人员，具备指导低职级同学的能力与经验。
+ Coding方面认可编程规范，注重团队合作。
+ 具有很强的学习能力，一直在向 Web 全栈开发不懈努力。
+ 具有良好的产品嗅觉，对用户体验的好坏有较好的判断能力。
+ 其他：喜欢踢足球、看电影、读书、金融理财。为人乐观，待人真诚，喜欢帮助别人。

<img src="/images/wechat/qrcode_for_gh_1bc1fa020fc5_344.jpg" style="width: 200px;" alt="微信扫描二维码关注博主的公众号（但我可能更喜欢写博客）">
