---
title: 最近的基金定投理财小结
date: 2019-11-11 23:51:00
tags: 理财
category: 理财
---

## Basics

从去年 10 月份开始进行基金定投，到现在已经有 440 多天了。定投的资金来源主要包括两个，一是每月的工资收入，二是之前在支付宝买的定期理财产品到期赎回。2019 年初，错过了第一波反弹的高收益率，才发现不断地买买买很容易，但是何时卖出却是最难的。人呐，很容易变得贪婪呢。今年 4 月份之后又有几次反弹，自己就陆陆续续地卖掉了一些基金，落袋为安。今年到上周五（2019-11-08）为止的收益率达到了 14%，还算不错，跑赢了所有的定期理财以及绝大部分 P2P 产品。

<!-- more -->

<img src="/images/2019/11/fundations-earnings.jpg" style="width: 400px;" />

不过也不是没有缺憾，主要有两个：

1. 在股市的低点，例如年初的 2440 点，没有大举买入。因为当时对基金定投还不是很有信心，手里也没有储备足够的现金流，所以错过了。
2. 整体的定投额度占比较低。目前自己的大部分收入依然是购买了定期理财产品，可能跟自己的风险偏好较低有关。其实应该再拥抱一些风险的。

## 说说交银海外中国互联网指数这个基金

交银海外中国互联网指数其实是自己最早买入的一个基金。不过当时还不懂定投，对于相关的概念、操作都稀里糊涂的。后来对这个基金每天 50 块钱进行定投，坚持了 13 个月。我觉得这个基金虽然选择的公司很好，但仍有个致命缺陷，即由于它购买的是海外股票，因而不能实时查看净值变动以进行买入卖出的操作。于是在收益率超过 6% 之后的某一天，清仓了。卖掉后算了一下，实际的年化收益率达到了将近 6%，所以还不错，这波操作不亏，哈哈。具体的计算见下面的代码。

```javascript
/**
 * 交银海外中国互联网指数收益情况计算
 */
// 末期总金额
var total = 12758;
// 总收益
var profit = 757;
// 投入的本金
var base = total - profit;
// 总收益率(13个月)
var profitRate = (profit / base * 100).toFixed(2);
console.log('13个月收益率:', profitRate, '%');
console.log('年化收益率:', (profitRate * 12 / 13).toFixed(2), '%');
```

- 13个月收益率: 6.31 %
- 年化收益率: 5.82 %

结果表明，此基比一般的一年期理财产品要好一点点（同期支付宝 1 年期理财产品最高的收益率应该不到 5%）。^_^

## 定投感想

定投久了，会产生一些感想，甚至想称之为“理论”，用来为自己的投资行为做决策、指导。例如：

- 现在的暂时高点，以后一定还会出现。所以，不着急卖出，尤其是收益率高但总额较小的时候。
- 现在以及过去了的低点，以后很可能不会再遇到了。所以，出现回调、大跌，就是买入的好时机。
- 过去的非理性高点，以后大概率不会出现。所以，不要期望等到大盘 5000 点再卖出，而是根据自己的预期收益率以及收益总额来决定是否卖出。

## 下一步的计划

操作方面，自己依然会坚持基金定投。然后针对目前存在的问题，主要做下面 3 个调整：

+ 一个是调整定投周期。之前自己是按照每天投几十块钱这样的频率来买基金的，这样其实有个坏处，即如果哪天想卖出了，还要考虑过去 7 天投入的钱（因为有较高的手续费）。所以现在改为了每周定投，这样非常方便卖出。而如果周中发现哪天大跌，还可以灵活补仓。
+ 其次是增加每周的定投金额。具体就不说了。
+ 最后，根据投资组合理论，增加购买的股票基金数量（目前同时定投的共 17 支），并且要同时包括客观型指数与主观型指数。客观型指数是指上证50、上证指数、沪深 300、中证 500、纳斯达克 100 指数等等这样的所谓“大盘”型指数；主观型指数是指以某一类主题、行业为背景选择的相关股票组成的指数，例如现在购买的中证银行指数、中证电子指数、新能源汽车主题指数、白酒指数、医疗医药主题指数等等。

另外，打算进一步补充更多的金融理财方面的理论知识。这个很简单，现在这个阶段通过读一些书就可以了。目前的书单（2020年底完成）：

1. 《富爸爸：给你的钱找一份工作》，富爸爸系列虽然啰嗦，但总是能找到一些提升认知的东西
2. 《富爸爸：投资指南》，主要是想看看其他人的投资思路
3. 《投资学》（原书第 10 版），金融领域教科书。
4. 《朱镕基讲话实录》，全四册。读这个系列的目的是加深对中国经济的宏观认知。