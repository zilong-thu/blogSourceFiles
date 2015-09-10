---
title: 用JMeter进行服务器压力测试心得
date: 2015-01-22 09:56:22
tags: JMeter
categories: 压力测试
sidebar: false
---
公司的网站项目要做压力测试，目的是了解现在的平台的并发能力。由于没有专业的压力测试工程师，于是我就被顶了上去，摸索了半个多月，搞明白了用apache-jmeter-2.12进行压力测试的流程。本文简记其中的心得体会。

<img src="/images/blog/2015/01/jmeter-logo.jpg" style="float: right; margin-left: 10px;"><a href="http://jmeter.apache.org/" target="_blank">Apache JMeter<sup>TM</sup></a>是一个纯Java写的开源程序，维护得还不错。其最初只是用于测试Web应用程序，不过后来扩展得越来越强大，可以开展多种测试。

压力测试的目的是对软件进行性能评估。开展压力测试可以帮助发现软件的性能瓶颈所在。

<!-- more -->

### 一个简单的Node服务器
下面的app.js为一个简单的HTTP服务器，为此需要准备一个html文件，命名为`segment-01.html`。

```javascript Node服务器app.js代码
var http = require('http');
var fs = require('fs');
var count = 0 ;

http.createServer(function(req,res) {
	count++;
	console.log('第', count, '次请求');
	
	fs.readFile('segment-01.html', function(err, html){
		if (err) {
			console.log('出错了');
			res.writeHeader(500, {"Content-Type": "text/plain"});
			res.end('Internal Server Error.');
		}else{
			console.log(count, ' 访问磁盘文件');
			res.writeHeader(200, {"Content-Type": "text/html"});
			res.end(html);
		}
		
	});
	
}).listen(1337);

console.log('服务器运行在1337端口');
```
### 手动添加HTTP请求
右键线程组->添加->Sampler->HTTP请求，然后写入该请求的主机或IP、端口号、请求的路径、请求方法等。

<img src="/images/blog/2015/01/jmeter-http-01.png">

###测试脚本录制
如果要测试的页面请求较多，或者不清楚具体的请求路径、请求参数，也可以使用脚本录制功能。方法如下：

首选更改代理服务器设置：Internet属性->连接->局域网(LAN)设置对话框中，将代理服务器打开。

然后在JMeter中为工作台添加“非测试单元->HTTP代理服务器”，并把Test plan content 的目标控制器选择为当前的线程组。

<img src="/images/blog/2015/01/jmeter-http-02.png">

在JMeter的“HTTP代理服务器”页面，点击“启动”后，在浏览器中进行操作，其所发起的所有HTTP请求（包括请求参数、发送的数据）都会被记录下来。

### 线程组设置
<img src="/images/blog/2015/01/jmeter-thread-01.png">

每个线程组可以包括多个请求。线程组中的请求是按照顺序依次发起的，并且直到上一个请求的响应返回后，才会进行下一个请求。而各个线程则是同时进行的。

一般要设一个合适的Ramp-Up Period，以使服务器有个“热身”的时间，让压力逐渐增加而不是一下子过来。

测试可以使用循环次数控制，也可以使用持续时间。我一般选择按持续时间来进行测试，持续时间要比热身时间大一个数量级，以尽可能消除热身阶段的误差（Ramp-Up阶段并不是真正的并发的），例如Ramp-Up Period设为了10s，那么持续时间要在110s以上才行。

### 测试结果

__吞吐率（Throughput） = 完成的请求数 ÷ 经历的时间 [单位：req/sec]__

下面是在工作用的台式机上对本地的Node服务器程序进行压力测试的结果。当并发数在2500及以上时，错误率较高，测试难以持续，因此并发数到2000为止。

<img src="/images/blog/2015/01/node-throughput.png">

每个请求的平均响应时间

<img src="/images/blog/2015/01/node-average-res-time.png">
