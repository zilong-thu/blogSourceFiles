---
title: Web前端性能优化实践
date: 2015-01-03 21:51:46
tags: Web性能优化
categories: Web-FE
sidebar: false
---

摘要： 本文记录了笔者所参与的一个网站平台开发中所采取的Web前端性能优化技术，对优化前后的性能进行分析对比，表明了Web前端性能优化的有效性和必要性。由于水平有限，针对该网站所作的测试肯定存在不科学、不合理的地方，不当之处请谅解。本文仅供相关技术参考之用。

关键字： Web前端；性能优化

<!-- more -->

## 1. 引言

Steve Souders（2008）<sup>[1]</sup> 提出的14条针对网站前端的性能优化技术，让开发者意识到网站的性能并不单单是在服务器端，前端的性能优化也是极其重要的。

> “如果我们可以将后端响应时间缩短一半，整体响应时间只能减少5%~10%；而如果关注前端性能，同样是将其响应时间减少一半，则整体响应时间可以减少40%~45%。”

## 2. 前端性能优化前的情况分析
### 2.1  Chrome Network瀑布图分析

开始性能优化之前，网站首次加载main.jsp时，共计加载了25个脚本文件、10个样式表文件以及46张图片，页面的总体积达到3.3MB（如图1所示）。公网10Mb带宽、无缓存情况下，页面完全加载所需时间为30s左右。同样的网络环境下，百度地图、淘宝网等典型网站的加载时间则分别为2s、7.75s（网站与百度地图的网站类型更为相近，之后的对比将主要针对百度地图）。

<figure style="text-align: center;">
<img src="/images/blog/web_fe/2015/01/pic_1.png">
<figurecaption>图1. 各网站静态资源加载对比（重点比较脚本、样式表）</figurecaption>
</figure>


图 1是针对 <a href="http://map.baidu.com/" target="_blank">百度地图</a>、<a href="http://www.tmall.com/" target="_blank">天猫商城</a> 的测试结果（2014年12月19日）。由该图可以看出，网站存在两个突出的问题：
+ 文件体积太大；
+ 脚本、样式表请求数太多。

### 2.2  YSlow评级
用Chrome浏览器的网络模块进行人工观察与分析，难免会有效率低下、分析不全面的问题。因此应当使用自动化的工具进行更深入的分析。

YSlow是雅虎公司开发的一个对网站前端性能进行分析的插件，最初作为Firefox浏览器的插件发布，后来也陆续有了Chrome等浏览器的插件。这里使用Firefox下的YSlow插件对指挥旅游首页性能进行分析，评级结果：优化之前的网站的评级，得分为71，评级C。而百度地图、天猫商城的得分、评级则分别是87 B、77 C。

在23个评级规则中，有4条未能达到A或B级（A意味着性能最优，F意味着有必要进行深入优化），它们应作为网站前端性能优化的主要入手点，分别是：
① F：减少HTTP请求次数
② C：使用内容分发网络（CDN）
③ F：添加较长的Expires头
④ F：使用gzip压缩组件
⑤ E：压缩JavaScript和CSS
⑥ F：Use cookie-free domains，使用无cookie的域名

## 3. 采取的前端性能优化技术
### 3.1 服务器开启Gzip压缩
Gzip最早由Jean-loup Gailly和Mark Adler创建，用于UNIX系统的文件压缩。后来成为Internet上数据压缩的常见格式。绝大多数现代浏览器都支持Gzip格式（体现在浏览器发送的HTTP请求头部的字段`Accept-Encoding`中）。一般来说，开启Gzip压缩之后，至少可以将数据传输体积减少50%，是加速网站的首要工作。

项目所采用的web服务器为tomcat，其开启Gzip压缩服务的方法如下。

在`{tomcat_root}/confrver.xml`文件中的`<Connector />`中添加如下代码：

```
compression="on"
compressionMinSize="2048"
noCompressionUserAgents="gozilla,traviata" 
compressableMimeType="textml,text/xml,text/javascript,text/css,text/plain, application/json"
```

开启成功之后，网站的HTTP响应头部中会使用`Content-Encoding`来确认响应已经被压缩。

Gzip压缩对网站性能的提升效果是很明显的，例如jquery-1.8.3.min.js的原始体积为91.4KB，Gzip压缩后的体积为33.4KB，压缩率（压缩后体积与压缩前体积之比）为36.5%；登录页面加载的全部文本资源大小为238KB，通过Gzip压缩，实际的传输体积仅为55KB，压缩率达到了23%。

### 3.2 使用GruntJS压缩合并组件

合并与压缩组件主要针对的是网站的JavaScript脚本与CSS样式表。通过合并文件，减少HTTP请求次数，然后对文件进行压缩以减少文件体积。前端开发中，可以使用GruntJS工具，使合并与压缩的工作更加自动化。

Grunt3是基于NodeJS的一个自动化压缩、合并、测试等构建工具，在安装Node环境后，通过npm进行安装。其相应的任务文件（Gruntfile.js）与依赖包应当放在项目的WebRoot目录下，并且添加到版本控制中。要执行压缩与合并任务，需要获取uglify、cssmin等组件。下面简介Grunt的使用方法。

(1). 安装NodeJS
(2). 安装 Grunt的CLI
    全局安装，可能需要管理员权限：`npm install -g grunt-cli`
(3). 在WebRoot目录下新建package.json，然后执行`npm install`命令，package.json文件内容如下：

```
	{
	  "name": "ZHLY",
	  "version": "0.1.0",
	  "devDependencies": {
	    "grunt": "~0.4.1",
	    "grunt-contrib-uglify": "~0.2.2",
	    "grunt-contrib-cssmin": "~0.5.0"
	  }
	}
```

(4). 新建Gruntfile.js，在里面配置要执行的任务（合并、压缩等）。如下配置Gruntfile.js文件（代码只为示意，略去详细的文件名）：

```
module.exports = function(grunt) {
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*压缩文件声明文本...*/\n'
    },
    build:{
     files:[{
      src: ['a.js', 'b.js', 'c.js'],
      dest: 'abc.min.js'
     },{
      src: ['d.js'],
      dest: 'd.min.js'
     }]
   }
  },
  cssmin: {
   options: {
     keepSpecialComments: 0
   },
   compress: {
     files: [{
       src: ["a.css", "b.css"],
       dest: 'ab.min.css'
     },{
       src: ['c.css'],
       dest: 'c.min.css'
     }]
    ]
   }
  }
});

  /* 加载任务插件 */
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-cssmin');

  /* 默认被执行的任务列表 */
  grunt.registerTask('default', ['uglify', 'cssmin']);
};
```

(5)在Gruntfile.js的同一目录下，运行`grunt`命令，就能得到想要的压缩文件。记得将项目中相应的资源引用更改为压缩后的文件名即可。
要注意的是，合并后的压缩文件的加载顺序应当与未压缩时的加载顺序一致，以避免出现例类似`undefined`的问题。

### 3.3  图片压缩与拼接

#### 3.3.1  图片格式的选取

网站登录页面的背景图按照1920×1080像素的尺寸进行设计。最初使用的格式为PNG，体积为2.1MB，有线和无线端的下载都很慢。更改为PNG-8后，体积减到1MB，但加载速度仍然很慢。最后更改为JPEG格式，输出质量为80%，图片的体积减小到260KB左右，图片的下载速度明显改善。

PNG、PNG-8、PNG-24以及JPEG格式图片的主要区别如下：

PNG格式适合对透明度有要求的情形；PNG-8处理不了复杂色域下的渐变，PNG-24可以几乎不失真地保留渐变。但是对于色域很广、对透明度没有要求的图片（准确地说是照片），应该毫不犹豫地采用JPEG格式。

因此，大背景图应当首选JPEG格式，而页面中的应用图标则应当使用PNG格式（或者PNG-24）。GIF格式适用于呈现动图，但因其色域窄、质量差，所以其他静态图都尽量使用PNG而非GIF。

#### 3.3.2  图片压缩方法

使用Photoshop自带图片压缩的选项，菜单为：文件--存储为Web所用格式。一般而言，JPEG选择输出质量为70~80%可以保证视觉上几乎没有差异，当然要因图片而异，有时输出质量为原图的30%也可以保证较高的视觉质量。
还有一个专门对JPEG格式的图片进行有损压缩的工具：JPEGmini <sup>[4]</sup>。

#### 3.3.3  图片拼接：CSS Sprites

CSS Sprites（CSS图片拼接，或CSS精灵图）利用的是CSS `backgrond-position`属性的特点：可以自定义背景图片相对于容器视口的坐标位置。使用这种技术，可以将许多小图片放到同一张图片里面，从而减少了HTTP请求，提高网页加载速度。
代码示意： `background-position: x y;`

### 3.4  添加Expires头

现代浏览器通常会默认使用缓存来减少HTTP请求的数量，并减小HTTP响应的大小，使Web页面加载得更快。Web服务器使用`Expires`头来告诉浏览器它可以使用一个组件的当前副本，直到指定的时间为止。这样在第二次试图请求某个资源时，便可能从浏览器的缓存中读取，避免了网络传输，从而加快页面加载速度。

根据Apache Tomcat文档的说明，在 `{tomcat_root}/conf/web.xml` 文件中的相应位置添加如下代码：
```xml
<filter>
  <filter-name>ExpiresFilter</filter-name>
  <filter-class>org.apache.catalina.filters.ExpiresFilter</filter-class>
  <init-param>
    <param-name>ExpiresByType image</param-name>
    <param-value>access plus 1 month</param-value>
  </init-param>
  <init-param>
    <param-name>ExpiresByType text/css</param-name>
    <param-value>access plus 1 week</param-value>
  </init-param>
</filter>
...
<filter-mapping>
  <filter-name>ExpiresFilter</filter-name>
  <url-pattern>/*</url-pattern>
  <dispatcher>REQUEST</dispatcher>
</filter-mapping>
```
可以为图像（`image`）、样式表（`text/css`）、脚本（`application/javascript`）等静态文件的HTTP请求响应头添加 `Expires`和`Cache-Control: max-age=` 头信息。

过期时间的选择应当依据项目开发进度、网站更新频率、用户使用频率等来决定。例如，图片通常是很少进行修改的，而脚本、样式表的更新则较为频繁，考虑到是在开发阶段，因此图片的过期时间设为1个月，而脚本、样式表的过期时间设为1周。

### 3.5  缩短DNS查找时间

<figure  style="text-align: center;">
<img src="/images/blog/web_fe/2015/01/pic_dns.jpg">
<figurecaption>图3. DNS解析过程示意图</figurecaption>
</figure>

现代浏览器通常具有DNS预解析（`DNS Prefetch`）功能，即加载网页后对页面中的超链接进行DNS解析并将结果缓存在浏览器中，便于用户点击时减少DNS解析时间（目前的DNS解析时间通常为200ms）。但是对于应用型的Web页面（或称单页面应用）而言，对页面内的超链接进行DNS预解析并无太大意义，因为几乎没有超链接。重要的是第一次执行DNS解析时的性能。

域名管理中的TTL（Time to Live）字段用于高速远程域名服务器可以缓存本记录多长时间。根据Andrew等人（2012）的建议，如果服务器的IP非常稳定（例如多年不变），那么将TTL值设为1天可能是安全的；而对于波动比较大的信息，几秒钟或一分钟后清除掉记录的做法或许更安全。

在购买到网站域名后，最初是将其TTL设为10分钟。这样在经过较短的时间（几个小时）后，该域名就已经被大部分的DNS服务器记录下来了。现在考虑到本系统的IP为固定IP，主机也在自己的机房内，而且没有备份，因此将TTL设大一点是可以的。目前将TTL值设为了1小时，可以使某个用户在1个小时内再次访问或请求资源时不必再进行完整的DNS解析，而是使用缓存结果。

### 3.6  其他技术

其他优化技术包括目前已成为业界实际标准的做法，例如：

① 将脚本引用置于页面底部；
② 将样式表引用置于页面顶部；
③ 不在页面中缩放图片；
④ 避免CSS表达式；
⑤ Ajax请求尽可能使用GET方式；
等等。

这些在此不做详细分析。

## 4. 前端性能优化结果评估

### 4.1 页面体积与加载时间

#### 4.1.1 页面体积

浏览器视口分辨率：1920×746像素。图片的优化只是针对HTTP请求次数，其前后尺寸变化不大，因此暂不考虑优化前后的差别。JavaScript请求数量“*+4”表示其中4个请求来自其他域（主要是搜狗地图服务）。

优化前后文件体积压缩率 = 优化后的体积÷优化前的体积

<table class="table table-bordered"><tbody><tr><th rowspan="2"></th><th colspan="2">优化前</th><th colspan="2">Grunt之后</th><th colspan="2">Gzip之后</th><th rowspan="2">优化前后文件体积压缩率</th></tr><tr><td>请求数</td><td>体积</td><td>请求数</td><td>体积</td><td>请求数</td><td>体积</td></tr><tr><td>JS</td><td>21+4</td><td>2600 KB</td><td>8+5</td><td>1037 KB</td><td>8+5</td><td>375 KB</td><td>14.4%</td></tr><tr><td>CSS</td><td>11</td><td>464 KB</td><td>10</td><td>382 KB</td><td>10</td><td>80.4 KB</td><td>17.3%</td></tr><tr><td>Total</td><td>110</td><td>3300 KB</td><td>75</td><td>1903 KB</td><td>75</td><td>791 KB</td><td>24.0%</td></tr></tbody></table>

在这里，压缩后的JS文件平均大小为80KB，CSS文件的平均大小为38.2KB。

文件压缩之后究竟应该多大才算合适？目前也无定论，只能根据经验来判断。合并后的文件如果太大，则对单个文件的下载、解析、执行的耗时就越长（当然，随着浏览器能力的不断提升，这个时间相比于网络传输时间仍是小量）。<a href="http://book.douban.com/subject/25856314/" target="_blank">《Web性能权威指南》</a>里提到：

> 谷歌PageSpeed团队的测试表明，30~50KB（压缩后）是每个JavaScript文件大小的合适范围：既大道了能够减少小文件带来的网络延迟，还能确保递增及分层式的执行。
> page 176.

#### 4.1.2 页面加载时间

10Mb带宽下，操作系统Windows 8 x64，内存8GB，CPU Intel i5，屏幕分辨率1366×768像素，使用Chrome浏览器（主版本号38）于2014-12-25 21:08至21:45，每隔1~2分钟对网站主页面/main.jsp进行无缓存加载时间测试（每次刷新页面之前，都先执行清空浏览器缓存的操作）；同时，也对第二次加载的时间进行了记录，以作为有无缓存的性能对比。详细结果如图 4所示。由图可见，经过优化后，页面无缓存的情况下完全加载时间为3.31秒，比优化之前减少了一个数量级，加载速度有很明显的提升。

<figure  style="text-align: center;">
<img src="/images/blog/web_fe/2015/01/pic_3.png">
<figurecaption>图4. 网站优化前后页面加载时间对比</figurecaption>
</figure>

###4.2  YSlow评级

在实施了上面的优化措施之后，用YSlow工具对网站主页面进行评级，得分80，等级为C，得分为F的项目由6降为3个，与优化之前相比有所提高。
YSlow给出的结果表明网站仍存在可以继续优化的方面，具体见下节。

## 5. 总结与展望

网站前端性能优化中最首要的原则是：优先针对瓶颈进行优化。所谓瓶颈，乃是制约性能的最关键因素。对于网站，其第一瓶颈为Gzip压缩未开启。开启压缩之后的瓶颈，则是组件未压缩。对于中小型网站来说，解决了前几个瓶颈问题，性能问题也就解决了一大半。

YSlow评级仍为3个方面给出F评分：
① F：减少HTTP请求次数；
② F：添加较长的Expires头；
③ F：Use cookie-free domains，使用无cookie的域名。

其中，①是仍需继续优化的方面；至于②，由于使用的CDN和搜狗地图服务对其资源并未添加Expires头，导致本项评分较低，但本域下的资源的Expires头都已经符号符合其评判标准了；而对于②，由于目前服务器只申请了一个域名，静态资源大都从该域名下获取，导致评分较低。这些都是未来可以进一步开展优化的方面。

### 5.1 详解Use cookie-free domains

> HTTP 1.0增加了请求和响应头部，以便双方能够交换有关请求和响应的元信息。最终，HTTP 1.1把这种格式变成了标准：服务器和客户端都可以轻松扩展首部，而且始终以纯文本形式发送，以保证与之前的HTTP版本的兼容。
> 今天，每个浏览器发起的HTTP请求，都会携带额外500~800字节的HTTP元数据：用户代理字符串、很少改变的接收和传输首部、缓存指令等等。有时候，500~800字节都说少了，因为没有包括最大的一块：HTTP cookie。现代应用经常通过cookie进行会话管理、记录个性选项或者完成分析。综合到一起，所有这些未经压缩的HTTP元数据经常会给每个HTTP请求增加几千字节的协议开销。
> ——《Web性能权威指南》

对于本项目这种需要用户名、密码验证登陆的网站，观察其主域下所有资源的请求头（包括document、css、javascript、images等），会发现全部的HTTP请求头部（Request Headers）中都把该域下的Cookie给发送过去了，即使服务器不需要。而且这个cookie还是未经任何压缩的。

> 浏览器执行“每个服务端最大连接数”的限制是根据URL上的主机名，而不是解析出来的IP地址。
> ……
> 这对那些想把内容分配到多个域的人来说是个好消息，他们不必额外部署服务器，而是为新域建立一条CNAME记录。CNAME仅仅是域名的一个别名。即使域名都指向同一个服务器，浏览器依旧会为每个主机名开放最大连接数。
> ——《高性能网站建设进阶指南》

目前很多网站的常见做法是分离图片，即让网站的文本资源从域1下载的同时，让所需的全部图片资源从域2开始并行下载。

此技术的具体实施方法：基本思路是在域名解析中添加CNAME记录，然后在工程中将资源分离；具体如何写代码，【待研究】。


## 参考文献

[1]. Steve Souders. High Performance Web Sites [M]. 
[2]. YSlow官网，http://yslow.org/
[3]. Grunt中文网，http://www.gruntjs.net/
[4]. JPEGmini官网，http://www.jpegmini.com/
[5]. Andrew S. Tanenbaum, David J. Wetherall著, 严伟, 潘爱民 译. 计算机网络（第五版）[M]. 北京：清华大学出版社, 2012. 
[6]. Ilya Grigorik. High Performance Browser Networking [M]. 李松峰 译. Web性能权威指南 [M]. 北京：人民邮电出版社, 2014.
[7]. Steve Souders. Even faster Web Sites: Performance Best Practice for Web Developers [M]. 口碑网前端团队 译. 高性能网站建设进阶指南, 北京: 电子工业出版社, 2010.