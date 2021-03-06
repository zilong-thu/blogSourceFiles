---
title: 我做前端面试官的一些心得
date: 2018-04-23 15:34:08
tags: Web-FE
categories: Web-FE
---

2017年4月份至今，我在猫眼担任前端社招或校招一面的面试官，陆陆续续面试了六十人左右。最开始我也是跟候选人一样紧张，而且还需要带着自己已经写好的题库去，后来就慢慢得心应手，也不需要带电脑，拿着纸和笔就可以去跟候选人聊天了。

最近曾指导一位同事开展面试工作，看到他面临的困惑，回想起自己刚做面试官时候的场景，感觉有必要整理一下自己的前端面试官心得体会，以便其他的新面试官了解通过 1 个小时的面试来考察候选人的前端/网络/算法等相关技术能力时应该要注意的事项。

下面的内容既适合初做前端面试官的同学阅读，作为参考；也适合正在找工作的前端同学了解到面试官所关注的东西，从而更好地应对前端面试。

自己的水平、经验有限，或有不当之处，欢迎指出。感谢！

<!-- more -->

### 奠定基调：聊天而非考试

面试的过程更应像是轻松的聊天，而非紧张的考试。要尽量营造宽松的气氛。很多面试官会让候选人做一下自我介绍，作为暖场。这样也不错。不过再细细想来，这种暖场方式显得面试官很不专业——简历早就发给你了，还要候选人自我介绍？早干嘛去了？所以也可以跳过自我介绍，而是按着简历，从头过一遍，问一些细节上的困惑，好比目前是否在职，候选人是如何选择前端这个方向的，平时是如何提高自己技术能力的（考察学习习惯），候选人的未来职业规划，或者候选人目前对自己的前端技术评估（初级，中级，高级，资深，专家？），了解一下候选人自认为在前端领域擅长的技能或知识点（从而方便后续选择题目进行面试），问一下候选人最近在读的书是什么，等等。

既然是聊天，那么在抛出每个问题的时候，务必要让候选人明白你的意思。如果候选人似乎无从下手，应该咨询一下是不是哪个地方没有讲清楚——面试官作为需求方，有义务说清楚自己的需求细节。

有些不错的面试官会让候选人先来做个编程题或者算法题，来热一下身，然后自己趁此时间看一下简历，或者帮候选人倒杯水。这种开场方式也不错。

### 不必立即给出否定反馈

如果候选人对于某个问题回答有点偏差，而且一看就是明显的不了解、不清楚，甚至在瞎编，那也不用立即指出对方的错误，只需要简单的回应“嗯，好”，“OK”，就可以了。因为立刻指出其错误，很可能会导致候选人情绪低落，面试流程受阻。

即使候选人基础真的很差，也不必在面试过程中发出否定反馈。可以适当缩短面试时间，在结束后，作为建议反馈给候选人。

### 一个代码题示例

> Talk is cheap, show me the code.
> （翻译：能动手咱尽量不吵吵。）

前端技术面，务必要写代码题。 你可以设定一个自认为难度适中的题目，看看候选人回答的情况如何，然后根据结果来决定后续的提问是探底，还是寻找其上限。

每个代码题都应该是可以由浅入深进行提问的。下面以我经常问的一个题目为例，展示一下循序渐进的技术问题面试过程：

```javascript
// 请用 JavaScript 实现一个构造函数 Foo
// 该函数的每个实例为一个对象，形如 {id: N}
// 其中 N 表示该实例是第 N 次调用 Foo 得到的。例如：

var a = new Foo();  // => {id: 1}
var b = new Foo();  // => {id: 2}
```

最简单的思路是借助于全局变量，像这样：

```javascript
var id = 1;

function Foo() {
  this.id = id++;
}
```

如果候选人能够写出上面的代码，就可以进一步问：如何不借助于全局变量来实现此功能？很明显，要借助于 IIFE 了。

```javascript
var Foo = (function(){
  var id = 1;

  return function() {
    this.id = id++;
  }
})();
```

如果候选人写出上面的代码，那说明他的 JavaScript 还不错，似乎可以考察得更深入一点：用户有可能在创建实例时忘记通过 new 关键字进行调用，请问该怎么避免这种潜在的风险？

这就涉及到作用域安全的构造函数了。候选人如果能够像下面这样写，就说明很不错：

```javascript
var Foo = (function(){
  var id = 1;

  return function() {
    if (!(this instanceof Foo)) {
      return new Foo();
    }

    this.id = id++;
  }
})();
```

嗯，如果候选人很快写出来，说明他的 JavaScript 功底真的真的很不错，看来有必要继续深入问一下：假如我希望另外一个构造函数 Sub，继承自 Foo，应该怎么做呢？这里考察的是 JS 继承。

```javascript
// 方法一：构造函数继承
function Sub() {}
Sub.prototype = new Foo();
Sub.prototype.constructor = Sub;

//方法二：构造函数窃取
function Sub() {
  Foo.call(this);
}
Sub.prototype = Object.create(Foo.prototype, {
  constructor: {
    configurable: true,
    enumerable: true,
    value: Sub,
    writable: true
  }
});

// 方法三：使用 ES6 的新语法
class Sub extends Foo {}
```

候选人写出来了三种方法，很好！然后可以问个小问题，对于 `var s = new Sub();`，如何验证 s 是 Sub 或者 Foo 的实例？这里就是考察 `instanceof` 操作符了：

```javascript
s instanceof Sub;  // => true
s instanceof Foo;  // => true
```

如果能够提问到这里，基本已经可以判定候选人前端功底很优秀，是符合你要求的人选了！接下来就可以问其他的问题，来进一步考察了。

### 差不多就行了？NO!

我们通常让候选人手写或者上机写代码，应该要检查一下候选人写出来的代码，看一下是否有问题。不要觉得他写出来了就一定是对的，或者是“差不多”就行了。去寻找一下诸如这些细节：

+ 循环的边界条件
+ 关键的 API 的使用
+ 循环/递归是否能够正常结束
+ 有 if，有没有对应的 else？

这就要求面试官自己的代码阅读能力必须足够强。

### 给候选人提问时间

无论你是担任一面还是二面还是N面，都应该留出10分钟左右给候选人进行提问。这是一种平等对话机制的体现，既能够给予对方尊重，又可以据此了解到候选人的关注点。

候选人通常会问一些技术方面的问题，或者问组织架构，公司氛围等等。这时候，只要是不违反公司/法律规定，都可以坦诚地跟候选人讲。

有的候选人会比较直接地问“我是不是挂了？”，你也可以视情况来回答。如果候选人真的基础比较差，明显不符合公司用人要求，那么可以委婉地告诉他一面没有通过。正所谓长痛不如短痛，让候选人回家悬着、傻傻等待，不如在面试结束后及时反馈面试结果。当然，如果公司的面试流程里约定了是由HR进行通知，那就没办法了……

### 要有自己的题库

面试官要有自己的题库，以适应不同水平的候选人。对于前端来说，我上面给出的代码题算是中等难度的问题。常见面试题其实在网上或者书上都可以找到。所以我经常面完后无奈地感慨：答案都告诉你们了，就不能提前准备一下嘛……

对于刚毕业或者工作一年内的候选人，前端部分考察60%左右，然后再考察一下操作系统基础知识、算法与数据结构、学习主动性、培养潜力等。算法题不应该是冒泡排序这种难度，起码要快排、归并，如果很出色，就考察树、动态规划、贪心算法（面试官团队通常会有相应的难度标准）。

如果候选人工作两年到五年，第一轮面试是可以忽略算法问题的，直接深入考察各种前端技能就好。前端能力可以深入到框架、库的源码实现（例如 lodash 某些方法的实现，vue 的对象属性变化侦测原理，手写发布订阅模式，实现一个模板引擎，甚至可以问如何用 JavaScript 生成 JavaScript 的 AST，等等）。

所有的考察，都应该是首选写代码。因为道理谁都懂~~

### 面试标准

+ 面试结果通常就这些方面进行评估：
+ 前端技术能力
+ 沟通能力
+ 学习主动性、培养潜力
+ 通用计算机素养（算法、数据结构、计算机网络等）
+ 个人定位、职业规划

结果通常可以划分为这几个档（参考 Facebook）：

+ strong hire
+ hire
+ weak hire
+ weak no hire
+ no hire
+ strong no hire

评价结果在 weak hire 和 weak no hire 区间内的候选人通常让人比较纠结。可以让二面面试官继续考察，然后综合一二面表现，一起讨论后给出最终结论。

谷歌的面试标准有一条被描述为“在综合考虑当前技术能力及未来发展潜力后，合格的候选人应该不低于团队的平均水平”。除非团队另有声明，建议前端面试官在心中参考这样的标准。

还有一条常见的参考的标准是，“你是否愿意与这位候选人成为同事？”

### 礼节

对于每位候选人，要礼貌地接待。水、打印好的简历、笔、纸，这些都是需要面试官或者 HR 提前准备好的。如果 HR 有疏漏，那么面试官最好能够弥补一下。毕竟在面试过程中，你是公司的形象代言人；而且江湖很小，说不定哪天你就去候选人的公司面试了呢~~

### 结束语

面试别人的过程，也是自己不断成长学习的过程。感谢在猫眼时帮助过我的小伙伴们。

最后祝大家越来越厉害~~
