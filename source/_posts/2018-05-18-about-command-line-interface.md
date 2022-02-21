---
title: 对命令行界面的认知变迁之路
date: 2018-05-18 00:06:50
tags: Linux
category: 随笔
---

### 大学：命令行是什么？好吃吗？

大学的时候，我的电脑运行的是 Windows 系统，从 XP 到 Vista 再到 Windows 8，都很少打开命令行界面。运行过最多次的 cmd 程序大概是为了自动关机：通常是电脑开着 uTorrent 做种（北邮人BT，做种攒积分，所谓的 BT 币？），宿舍断电不断网，看下电源，还能撑1个小时，于是输入如下命令：

```bash
shutdown -s -t 1800
```

然后回车，把电脑亮度调低，爬上床睡觉。上面的命令会让电脑在半个小时后自动关机，也可以让我高枕无忧。

除此之外，就很少使用 cmd 处理其他事情了。也许与自己不是计算机专业有关，缺少点文化熏陶。即便如此，生活也还过得去：游戏、QQ、上网、电影……每件事情都正常运转。用电脑做的正事儿也就是写写作业、小论文（很少是编程类的）。

<!-- more -->

### 实验室：跳坑与出坑

后来进了实验室。这时候写代码的时间渐渐多了起来，因为要给资本主义老板写软件卖钱嘛。我们最开始是用 Borland C++ 为基础库来开发图形用户界面程序，后来转为了 Qt。我很激动，因为百度后了解到 Adobe 和 Autodesk 的很多软件就是用 Qt 开发的。于是买了本《C++GUI Qt4编程》，对着书里 Hello World 示例代码一顿敲，然后用 Qt 的编译器 MinGW（Minimalist GNU for Windows） 进行编译。OK，弹出了漂亮的对话框！然后师兄走过来说，你这样是不对的，应该用 Visual Studio 去写代码，debug，以及编译。我恍然大悟，“原来是这样啊”。于是把书扔到一边，跳进了一个很深的坑里。

就这么度过了一段比较悲催的岁月。编程能力也没什么长进。这时候别说用命令行，我连自己拿了优的 C++（虽然是选修课） 都开始怕了起来……当时实验室里有个大神，系统用的是 Linux，精通 C++，每天的工作在我看来就是敲各种命令。非常神奇。

现在来看，倒不是说 Visual Studio 有问题，而是自己没能深入理解所用的编程语言特性导致的。后来趁着给老板做网站的机会，我听从大神的建议，以及吸取自己在 C++ 路上的惨痛教训，开始认真学 JavaScript，先把语言功底打得很扎实。这次就顺利多了。

### 工作后：渐入佳境

去南京工作以后，写代码成为了在公司占据时间最多的事情。回忆起大神当年的各种操作之酷炫，我决定也经常用命令行。公司的电脑是 Windows，我就装了个 bash；自己的原来的电脑快废了，后来有了点积蓄，买了台 Macbook Pro。用命令行做什么呢？一个是 Git，以 Github 为代码仓库，管理自己的一些源文件，例如博客；另外，这个时候接触 NodeJS 也多了起来，用 npm 进行包管理，这个就只能在命令行界面下操作了。此时渐渐地不再怵命令行，也慢慢地感受到 CLI 带来的效率提升。

<img src="/images/2018/05/cli-demo.png" style="width: 45%;" />

加入美团后，有个老司机做一对一导师，指点了很多迷津。命令行的使用技巧也得到了很大的扩展。除了更多地使用 Unix 自带命令（如 ssh，mkdir，mv，touch，find，chmod，等等），还知道了许多自由软件可供使用：Oh My Zsh，Iterm2，Homebrew，nvm，vim……以及后来自己发现的图片处理程序 ImageMagick、PNG 无损压缩程序 Zopflipng，乃至可以在命令行下播放音乐的 SoX：

```
# MacOS X 安装
brew install sox

# 单曲循环播放某个文件
# 支持 WAV,MP3,MPG,OGG,FLAC 等文件格式
play path/to/music/file repeat 0
```

### 思考：文化随机漂移

Eric S. Raymond 的《UNIX编程艺术》这本书里提到了操作系统的风格对软件设计、程序开发的影响，并且存在着“文化随机漂移”的现象——所谓“传统”，无非是“先入为主”罢了。

我们大部分人可能都是在以图形用户界面（GUI）为默认交互界面的操作系统下开始熟悉并长期使用电脑的（Windows，或 MacOS）。GUI 最大的优势是所有操作可以用户友好的参与，而最大的问题也在于此：所有的操作都需要有用户参与。这就难以完成批处理，以及自动化。命令行界面（CLI）则在批处理、自动化方面存在天然优势。

> 可视接口在处理小数量物体简单行为的情况下，工作得很好；但是当行为或是物体的数目增加时，直接操作很快就变成了机械重复的苦差。一个可以直接操作的接口，其缺点是一切都必须亲自操作。同一个发布高级指令的执行者相反，用户降落成了装配工，必须一遍又一遍地执行同样的任务。
>
> —— Gentner & Nielsen，《反 Mac 接口》

对于编程新手来说，最好一开始就有意识地在命令行界面下进行各种操作。可以参考《鸟哥的 Linux 私房菜（第三版）》这样的入门书籍。一开始可能会觉得费劲，因为要记忆许多命令。后来慢慢熟悉了就会快很多了。不过也好在绝大部分命令在 Unix/Linux 的各种发行版中都是一致的，所以不用太担心记忆这些命令是白花时间。

现在我越来越熟练于在 CLI 下进行各种操作，而且也似乎越来越无法离开它了。这也许是变老的一个象征吧 `^_^` 。

我自己从不排斥 GUI，现在变得更加喜欢 CLI。而我心目中“好的操作系统”，应该要能够对二者都很好地支持。目前来看， MacOS X 算一个；最新版 Ubuntu 看起来也很不错（只是我很久不用了）。CLI + GUI 的理念似乎也让微软颇为心动，他们甚至在 Windows 10 里加入了 Bash 呢（2016年）。

<img src="/images/2018/05/bash-in-win10.jpg" style="width: 70%;" />

### 参考资料

1. [http://www.mingw.org/](http://www.mingw.org/)
2. Eric S. Raymond，《UNIX编程艺术》
3. [Microsoft is bringing the Bash shell to Windows 10](https://techcrunch.com/2016/03/30/be-very-afraid-hell-has-frozen-over-bash-is-coming-to-windows-10/)