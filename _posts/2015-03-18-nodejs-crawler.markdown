---
title: NodeJS爬虫程序
date: 2015-03-18 18:24:39
tags: 网络爬虫
categories: NodeJS
sidebar: false
---
出于工作需求，要获取到足够多的网页数据给Hadoop研发人员做Demo用。自己简单试了试用NodeJS编写网络爬虫，初步感觉还不错，挺有意思。

<!-- more -->

## 准备工作

### 网络爬虫基本原理

[这篇文章：网络爬虫基本原理(一)](http://www.cnblogs.com/wawlian/archive/2012/06/18/2553061.html)其实讲解地已经比较清楚了，尤其是那张图，在此引用一下：

<img src="/images/blog/2015/03/crawler_01.png" class="img-invert">

使用NodeJS写的网络爬虫已有不少示例（例如[Create a simple web spider in node.js  by Licson](http://licson.net/post/create-a-simple-web-spider-in-node-js/)），思路大体一致，使用的包也大同小异，重点是要搞清楚：想从页面中获取什么？自己关心什么？然后要据此使用特定的正则表达式等词法分析手段。

### 安装爬虫程序的依赖包
主要安装的是两个模块：`request`模块、`cheerio`模块。前者用于发送HTTP请求，后者用于根据获取到的HTML数据创建一个DOM结构，从而可以对其进行类似jQuery的操作。

package.json文件内容如下：

```javascript package.json文件
{
  "name": "crawler",
  "version": "0.0.1",
  "private": true,
  "author": "Wang Zilong",
  "dependencies": {
    "request": "~2.53.0",
    "cheerio": "~0.18.0"
  },
  "engines": {
    "node": "0.10.x",
    "npm": "1.3.x"
  }
}
```

## 小试牛刀
### 抓取豆瓣电影
下面的程序可以工作，使用的Node.js版本是 0.12.0。不过，对豆瓣请求过于频繁的话，它会对IP进行封禁。

该脚本以几个“种子URL”为入口，获取到一个页面后，先保存，然后解析该页面的HTML中具有相同结构的超链接（使用[`cheerio`模块](https://www.npmjs.com/package/cheerio)），对比已经检索过的页面的链接，如果不存在，则对其发起请求（基于[request模块](https://www.npmjs.com/package/request)）。

```javascript 程序段-01-豆瓣电影网页爬虫
var fs = require('fs');
var request = require('request');
var cheerio = require('cheerio');

var outStream = fs.WriteStream('urls.txt');

/* 精心挑选的种子URL——如何精心是个问题 */
var originalURL = [
        'http://movie.douban.com/subject/1292052/', /* 《肖申克的救赎》 */
        'http://movie.douban.com/subject/11026735/',  /* 《超能陆战队》 */
        'http://movie.douban.com/subject/3993588/'  /* 《狼图腾》 */
    ];

/* 去重字典 */
var urlDic  = {};
var urlList = [];

/* 传入一个可能带有参数字符串的链接，返回一个无参数的链接 */
var Tools = {};
Tools.getUrl = function(href){
    var index = href.indexOf('?');
    var url = href;
    if (index > -1) {
        url = href.substring(0, index);
    }

    return url; 
};

Tools.getNumbersOfUrl = function(href){
    var pattern = /\d+/;
    var numbers = pattern.exec(href);
    return numbers;
}

/* 根据传入的url，获取该页面，并提取该页面中的类似URL地址并去重 */
fetchNextURLs = function(url){
    request({url: url}, function (error, response, body) {
        if (error) {
            return console.error(error);
        }
        console.log('成功爬取到页面： ' + url );

        var $ = cheerio.load(response.body.toString());

        /* 保存当前页面 */
        var numbers = Tools.getNumbersOfUrl(url);
        var htmlStream = fs.WriteStream('./douban_movies_html/movie'+numbers + '.html');
        htmlStream.write(body);
        htmlStream.end();

        /* 获取当前页面包含的所有URL，去重后放入hrefs列表 */
        var hrefs = [];
        $('#recommendations dt a').each(function(){
            var $me = $(this);
            var href = Tools.getUrl( $me.attr('href') );
            var numbers = Tools.getNumbersOfUrl(href);
            if(!urlDic[numbers]){
                urlDic[numbers] = true;
                hrefs.push(href);
                outStream.write(href+ '\r\n');
            }
        });

        /* hrefs的长度为0，表明无法继续查找新的链接了，因此停止爬虫程序 */
        if(hrefs.length === 0){
            console.log('本页面未能爬取到新链接。');
        }else{
            urlList.concat(hrefs);
            /* 如果没有超过预定值，则继续进行请求 */
            if(urlList.length< 100){
                for (var i = 0; i < hrefs.length; i++) {
                    fetchNextURLs(hrefs[i]);
                }
            }else{
                outStream.end();
                console.log('超过预订的数目，爬虫程序正常结束。获取到的总链接数为：', urlList.length);
            }
        }       
    });
};

/* 根据种子URL启动爬虫 */
for (var i = 0; i < originalURL.length; i++) {
    fetchNextURLs(originalURL[i]);
}
```

## 明确种子URL来源：利用网站提供的页面
有的站点实际上是自己提供种子URL的，例如京东商城的“全部分类列表”，点击其中任何一个分类（具有`http://list.jd.com/listId.html`格式），就可以得到一大批“优质”的链接（具有`http://item.jd.com/id.html`）。然后就可以利用类似上面的递归，来一个个获取页面。代码如下。

```javascript 程序段-02-京东商城货品页网页爬虫
var fs = require('fs');
var request = require('request');
var cheerio = require('cheerio');

var outStream = fs.WriteStream('urls.txt');

/* 去重字典 */
var urlDic  = {};
var urlList = [];

/* 针对京东商城的商品页面进行设计的爬虫程序 */

/* 获取种子URL */
getSeeds = function(url){
    request({url: url}, function (error, response, body){
        if (error) {
            return console.error(error);
        }

        console.log('得到种子页面： ' + url );

        var $ = cheerio.load(response.body.toString());

        $('a[href*="list.jd.com/"]').each(function(){
            var $me = $(this);
            var href = $me.attr('href');
            fetchItemURLs(href);
        });
    });
};

/* 根据传入的url，获取该页面，并提取该页面中的类似URL地址并去重 */
fetchItemURLs = function(url){
    request({url: url}, function (error, response, body) {
        if (error) {
            return console.error(error);
        }
        console.log('成功爬取到页面： ' + url );

        var $ = cheerio.load(response.body.toString());

        /* 保存当前页面 */
        var pattern = /\d+-?\d+-?\d+/;
        var numbers = pattern.exec(url);
        var htmlStream = fs.WriteStream('./jd/item'+ numbers+'.html');
            htmlStream.write(body);
            htmlStream.end();

        /* 获取当前页面包含的所有URL，去重后放入hrefs列表 */
        var hrefs = [];
        $('a[href*="item.jd.com/"]').each(function(){
            var $me = $(this);
            var href = $me.attr('href');
            var pattern = /\d+/;
            var numbers = pattern.exec(href);
            if(!urlDic[numbers]){
                urlDic[numbers] = true;
                hrefs.push(href);
                outStream.write(href+ '\r\n');
            }
        });

        /* hrefs的长度为0，表明无法继续查找新的链接了，因此停止爬虫程序 */
        if(hrefs.length === 0){
            console.log('本页面未能爬取到新链接。');
        }else{
            urlList.concat(hrefs);
            /* 如果没有超过预定值，则继续进行请求 */
            if(urlList.length< 1000){
                for (var i = 0; i < hrefs.length; i++) {
                    fetchItemURLs(hrefs[i]);
                }
            }else{
                outStream.end();
                console.log('超过预订的数目，爬虫程序正常结束。获取到的总链接数为：', urlList.length);
            }
        }       
    });
};


/* 根据种子URL启动爬虫 */
getSeeds('http://www.jd.com/allSort.aspx');
```

### 添加请求头
京东的商品页面可以随便爬，不会对爬虫进行禁止（这也反映了该网站服务器集群性能的优异）；但豆瓣就不一样了，我用2.1的程序爬之，获取了几百个页面，然后豆瓣就禁止该爬虫访问了。

解决的办法有：减小并发数；伪造成浏览器。后一个方法比较简单，如下。

```
/* 给request的选项中添加 headers 属性，把User-Agent字段加入到每个HTTP请求的头部。
 * 这样一来，豆瓣就会认为这是浏览器发起的一个请求从而不会拒绝响应了。 
 */
request({
        url: url,
        headers: {
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
            'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.99 Safari/537.36'
        }
    }, function (error, response, body) {
```

### 程序停止、数据保存
运行后会注意到，已经获取到的页面实际上已经远远超过预定值1000了，但是程序依旧没有停止下来。

大爷的，原来是对JS的Array `concat`函数理解错了。`concat`操作并不是在原数组的上面进行的，而是返回一个新的拼接后的数组。

另外，每当抓取到一个页面，就应该把该页面的 URL 保存到文件中，使用`fs.appendFile()`方法即可。

最后，将预期的抓取页面数作为程序的`option`属性的一部分，当达到该值后，便调用`process.exit()`方法立即结束当前的node程序（目前会导致最后一个正在搜索的结果不能保存下来，不过在总数目很可观的情况下，这无伤大雅）。

经过这一番折腾，针对豆瓣电影的爬虫变成这个样子：

```javascript 程序段-03-豆瓣电影网页爬虫
var fs = require('fs');
var request = require('request');
var cheerio = require('cheerio');

/* 精心挑选的种子URL——如何精心是个问题 */
var originalURL = [
        'http://movie.douban.com/subject/1292052/', /* 《肖申克的救赎》 */
        'http://movie.douban.com/subject/11026735/',  /* 《超能陆战队》 */
        'http://movie.douban.com/subject/3993588/'  /* 《狼图腾》 */
    ];

var option = {
    targetNumber: 100000,  /* 预期的爬取页面数 */
    fileNames: {
        crawled: 'urlCrawled.txt',  /* 每次爬取到一个页面，就将其URL保存到该文件中 */
        allURLsFound: 'allURLsFound.txt'  /* 用于保存所有已知的URL */
    }
};
/* 数据记录 */
var urlDic  = {};
var data = {
    urlListAll: [],  /* 要爬取的连接数 */
    urlListCrawled: [],  /* 已爬链接 */
    countUrlCrawled: 0  /* 已爬链接数目 */
};
 
/* 传入一个可能带有参数字符串的链接，返回一个无参数的链接 */
var Tools = {};
Tools.getUrl = function(href){
    var index = href.indexOf('?');
    var url = href;
    if (index > -1) {
        url = href.substring(0, index);
    }
    return url;
};
Tools.getNumbersOfUrl = function(href){
    var pattern = /\d+/;
    var numbers = pattern.exec(href);
    return numbers;
}
 
/* 每爬取到一个页面，将其URL附加到一个txt文件中 */
Tools.saveCrawled = function(url){
    data.countUrlCrawled++;
    data.urlListCrawled.push(url);
    fs.appendFile(option.fileNames.crawled, url + '\r\n', function (err) {
        if (err) throw err;
    });
};
 
/* 在爬虫程序结束后，把爬取到的页面的URL写入文件 */
Tools.exitCrawler = function(){
    process.exit();
};
 
/* 根据传入的url，获取该页面，并提取该页面中的类似URL地址并去重 */
fetchNextURLs = function(url){
    request({
        url: url,
        headers: {
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
            'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.99 Safari/537.36'
        }
    }, function (error, response, body) {
        if (error) {
            return console.error(error);
        }

        Tools.saveCrawled(url);

        console.log('成功爬取到页面： ' + url );
        console.log('已爬页面数 = '+ data.countUrlCrawled );

        var $ = cheerio.load(response.body.toString());

        /* 保存当前页面 */
        var numbers = Tools.getNumbersOfUrl(url);
        var htmlStream = fs.WriteStream('./douban_movies_html/movie'+numbers + '.html');
        htmlStream.write(body);
        htmlStream.end();
 
        /* 获取当前页面包含的所有URL，去重后放入hrefs列表 */
        var hrefs = [];
        $('#recommendations dt a').each(function(){
            var $me = $(this);
            var href = Tools.getUrl( $me.attr('href') );
            var numbers = Tools.getNumbersOfUrl(href);
            if(!urlDic[numbers]){
                urlDic[numbers] = true;
                hrefs.push(href);
                fs.appendFile(option.fileNames.allURLsFound, href+ '\r\n', function (err) {
                    if (err) throw err;
                });
            }
        });
        /* hrefs的长度为0，表明无法继续查找新的链接了，因此不再递归 */
        if(hrefs.length === 0){
            console.log('本页面未能爬取到新链接。');
        }else{
            data.urlListAll = data.urlListAll.concat(hrefs);
            /* 如果没有超过预定值，则继续进行请求 */
            if(data.countUrlCrawled < option.targetNumber){
                for (var i = 0; i < hrefs.length; i++) {
                    fetchNextURLs(hrefs[i]);
                }
            }else{
                console.log('爬取到的页面数目已达到预期值...');
                Tools.exitCrawler();
            }
        }       
    });
};

/* 根据种子URL启动爬虫 */
for (var i = 0; i < originalURL.length; i++) {
    fetchNextURLs(originalURL[i]);
}
```

### 读取之前的结果
如果在程序开始的时候，先读取之前已抓取的页面、待抓取的页面的列表，那么整个爬虫程序就变成了支持“断点续爬”功能的了。

【待完成】

## 关于遍历算法
在2、3小节中的两段代码，都是深度遍历优先的。

深度优先思路很简单，代码也容易写，但是存在致命的缺点：深度未知。因而就容易使爬虫陷进自己挖的坑里，即所谓“爬虫陷入问题”。
