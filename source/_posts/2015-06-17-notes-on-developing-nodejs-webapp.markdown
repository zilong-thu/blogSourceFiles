---
title: NodeJS+Express+MySQL开发小记(2):服务器部署
sidebar: false
date: 2015-06-17 18:48:52
tags: 
- NodeJS
- Linux
- 服务器
categories: NodeJS
---

[NodeJS+Express+MySQL开发小记(1)](/2015/04/14/notes-on-nodejs-express-mysql-and-promise/)里讲过在本地搭建 NodeJS 网站的若干细节。本人最近在阿里云服务器上面按最低配租了4个月的云服务器，所以想试着把这个项目部署到云上。云服务器操作系统是Ubuntu 14.04 LTS。之前一直在Windows下做开发，对于Linux下的环境搭建、配置还不是很熟悉，搭建的过程中学到很多东西。

本文简单记录对服务器进行部署的一些细节方面与解决方案。

<!-- more -->

## 安装MySQL Server
直接通过`apt-get`就可以：

```
sudo apt-get install mysql-server
```

安装过程会提示 `After this operation, 96.4 MB of additional disk space will be used.`。MySQL的版本为`Server version: 5.5.43-0ubuntu0.14.04.1 (Ubuntu)`。

安装完成后，运行

```
mysql -u root -p
```

输入密码，即可进入MySQL的终端了。

## 用Git进行版本控制
出于跟风，以及提高技术的目的，毫不犹豫地选择了Git而不是SVN来进行代码版本控制。最初是使用Github来作为远程服务器，用了大概两个月，感觉对于开发这种业务性太强的项目（尤其要在代码里使用数据库的），使用开源的Github仓库来托管并不让人放心。

后来发现了国内的业界良心 [Git@OSC](http://git.oschina.net/) ，其宣布永久开放git公有库和私有库。相关新闻：

+ [Git@OSC 欢迎你！](http://www.oschina.net/news/40844/git-osc-welcome)
+ [OSC 全面永久开放 git 公有库和私有库](http://www.oschina.net/news/41842/git-osc-no-limitation)

此货的功能与Github并无太大区别，考虑到私有仓库的免费，便果断迁移了过来。

### 用Git部署代码
最开始是从Github上获取代码的：

```
cd /var
mkdir www
cd www
git clone https://github.com/zilong-thu/d.git debate
```

以后如果代码发生变动，可以这样更新代码：

```
cd debate
git pull origin master
```
后来远程代码仓库迁移到git@osc，那么可以添加这个新的remote，命名为osc，然后以后就只从osc进行代码获取了。以后提交代码，也就只往git@osc提交，就一切OK了。

```
git remote add osc https://git.oschina.net/zilong-thu/d.git
 
git pull osc master
```

如果要删除Github那个remote，可以这样：

```
git remote rm origin
```

### 使用SSH而非HTTPS
先生成一个SSH公钥，方法：

+ [Generating SSH keys | GithubHelp](https://help.github.com/articles/generating-ssh-keys/)

注意，如果是 Windows &/8 + Git Bash 环境，那么在将你的key添加到 `ssh-agent` 中时应该使用这个命令（参考 [Stack Overflow : Could not open a connection to your authentication agent](http://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent/20403535#20403535)）：

```
eval $(ssh-agent -s)
```

在用于开发的电脑上，修改本地代码库的remote url

```
git remote set-url origin git@github.com:USERNAME/OTHERREPOSITORY.git
```

参考

+ [Changing a remote's URL | GithubHelp](https://help.github.com/articles/changing-a-remote-s-url/)

以及一个中文的 Pro Git：

+ [4.3 服务器上的 Git - 生成 SSH 公钥](https://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)

## 运行服务

### 安装依赖

在`debate`目录下，安装node模块，执行

```
npm install
```

安装好所有依赖后，如下启动服务器：

```
npm start
```

使用阿里云给的IP加上端口号，即可访问到开启了的网站。

### 使NodeJS服务在后台运行

目前是使用putty使用ssh远程登录来操作服务器的，如果关闭这个putty，即会话终止，则服务也随即停止。解决思路自然是让程序在后台不间断地运行。解决方案可以参考：

 + [Linux 技巧：让进程在后台可靠运行的几种方法 | IBM Developerworks](https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/)

因此，在`/var/www/debate`目录下新建一个shell脚本`start.sh`，内容为：

```
# Debate server start shell script
nohup npm start &
```
`&`是为了让程序能够在后台运行。这时候`ctrl+c`，或者关闭putty会话，就都不会杀死这个服务进程了。

### 用supervisor自动重启服务
NodeJS代码发生变化时，一般是必须重启Node程序才可以生效的。每次手动重启的话太麻烦，可以使用专为NodeJS打造的[node-supervisor](https://github.com/petruisfan/node-supervisor)模块来实现代码变动时自动重启，以及服务崩溃时自动重启的功能。

其官网的描述是这样的：

> Node Supervisor is used to restart programs when they crash.
> It can also be used to restart programs when a *.js file changes.

务必要全局安装：

```
npm install -g supervisor
```

对于express 4.*版本，启动服务时是这样的：

```
npm start
```

它其实上会到`package.json`里面寻找这个：

```
"scripts": {
    "start": "node ./bin/www"
  }
```

因此执行的实际上是`node ./bin/www`。那么使用`supervisor`来代替`npm start`的话就应该是`supervisor ./bin/www`。因此，在shell脚本里可以这样写：

```
nohup supervisor ./bin/www &
```

如此一来，当更新代码时，就不必手动去重启NodeJS服务了。

## 停止服务
### 使用kill
先列出所有进程，找到Node服务的进程ID，将其杀死，即可停止服务了。

```
ps aux

root     11200  1.1  4.0 898452 20436 ?        Sl   Jun19  75:13 node /usr/local
root     11387  0.0 15.4 923264 77080 ?        Sl   Jun19   0:03 node ./bin/www

kill 11200
```
只要杀死`node /usr/local`开启的进程，`node ./bin/www`进程也随之死掉了。

###使用进程名+脚本来停止服务


## 端口
如果运行在未被占用的非80端口上，服务器一声不吭就给开了。但是如果用80端口，可能要注意一下权限的问题。最近都是使用root用户来开启Node服务并运行于80端口上，没有碰到什么问题。

使用80端口后，访问服务就不必加端口号了。

## 用shell实现自动化管理
